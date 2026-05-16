# Plano — Visualização de dados nas análises

**Feature:** Adicionar camada de visualização (radar V1, pizza V2, quadrante V3) às respostas do agente
**Goal:** Implementar D1-D6 do spec aprovado em [2026-05-15-visualizacao-dados-design.md](../specs/2026-05-15-visualizacao-dados-design.md), bumpar versão para 0.2.0
**Spec referência:** `docs/superpowers/specs/2026-05-15-visualizacao-dados-design.md` — toda decisão de conteúdo deve ser rastreável a uma das seções D1-D6 do spec
**Pré-requisito:** spec lido na íntegra antes da Task 1

## Arquitetura

| Arquivo | Tipo | Responsabilidade única |
|---|---|---|
| `tests/07-visualizacao-investimento.md` | novo | Test case: análise investimento gera V1+V2 em Claude |
| `tests/08-visualizacao-opt-in.md` | novo | Test case: pessoal/empresarial sem-pedido vs com-pedido |
| `tests/09-visualizacao-degradacao.md` | novo | Test case: Copilot declara degradação do radar |
| `frameworks/visualizacao.md` | novo | Catálogo V1/V2/V3, invariantes, paleta, receitas por plataforma |
| `frameworks/output-review-ready.md` | edição | Regras de localização dos visuais no markdown (D6) |
| `frameworks/investimentos.md` | edição | Gatilho automático (D1) + checklist de pré-requisitos (D3) |
| `frameworks/financas-pessoais.md` | edição | Opt-in explícito (D1) |
| `frameworks/financas-empresariais.md` | edição | Opt-in explícito (D1) |
| `AGENT.md` | edição | Protocolo de visualização opcional referenciando o novo framework |
| `guias-instalacao/claude.md` | edição | Capability: Artifacts disponíveis |
| `guias-instalacao/chatgpt.md` | edição | Capability: Code Interpreter requerido |
| `guias-instalacao/copilot.md` | edição | Capability: Mermaid-only, sem radar |
| `VERSAO.md` | edição | Bump 0.1.0 → 0.2.0 |
| `CHANGELOG.md` | edição | Entrada [0.2.0] |
| `tests/README.md` | edição | Adicionar testes 07/08/09 e mover critério de release para 0.2.0 |

## Stack

- Markdown puro (commonmark/GFM)
- Mermaid (renderizado pelas plataformas)
- Recharts via Claude Artifacts (referência conceitual no framework, código de exemplo no `visualizacao.md`)
- matplotlib via ChatGPT Code Interpreter (idem)
- Verificação: `git grep` e `Read` para confirmar presença de strings-âncora; checklist manual de comportamento

## Convenção TDD adaptada

Como o projeto não tem código executável, cada ciclo é:

1. **Red:** escrever (ou atualizar) o test case com expectativas verificáveis por grep no framework
2. **Confirma Red:** rodar grep nas strings-âncora do test → 0 matches (a "falha")
3. **Green:** editar o framework adicionando a string-âncora e o conteúdo de fato
4. **Confirma Green:** rodar o mesmo grep → ≥1 match
5. **Walkthrough:** ler o framework + test case lado a lado e confirmar que um humano executando o test contra um agente carregando o framework atualizado marcaria todos os ✅
6. **Commit**

Cada test case usa strings-âncora explícitas (ex: `"Catálogo: Radar (V1)"`) que devem aparecer literalmente no framework correspondente. Isso permite grep mecânico.

---

## Task 1: Test case 07 — investimento happy path

Files: `tests/07-visualizacao-investimento.md`

Steps:
- [ ] Write `tests/07-visualizacao-investimento.md` com este conteúdo exato:

      ```markdown
      # Teste 07 — Visualização em análise de investimento

      ## Setup

      Carregar `AGENT.md` + `frameworks/output-review-ready.md` + `frameworks/investimentos.md` + `frameworks/visualizacao.md`.

      Plataforma: Claude Projects (Artifacts disponíveis).

      Perfil mínimo preenchido em `estado-agente/perfil.md`:

      - Faixa etária: 30-39
      - Jurisdição: BR
      - Tolerância a risco: moderada
      - Horizonte: 10+ anos

      ## Entrada do usuário

      > Estou montando uma carteira: 40% renda fixa pós-fixada, 30% ETF amplo BR, 15% ETF S&P 500, 10% FIIs, 5% cripto. Compare contra a alocação de referência para moderado e me diga onde estou desalinhado.

      ## Comportamento esperado

      A saída deve conter:

      - [ ] Cabeçalho padrão + as 8 seções obrigatórias do `output-review-ready.md`
      - [ ] Dentro da **Seção 3**, após a tabela de scores, um **radar (V1)** renderizado como Artifact React (Recharts `<RadarChart>`), cobrindo no mínimo Risco / Retorno / Liquidez / Alinhamento
      - [ ] Caption do radar em itálico abaixo, **interpretativa** (descreve insight, não eixo)
      - [ ] Dentro da **Seção 6**, ou após premissa de composição na Seção 2, uma **pizza/donut (V2)** Recharts com 5 fatias (rendaFixa/ETF-BR/ETF-US/FII/cripto) usando a paleta determinística do framework
      - [ ] Caption interpretativa da pizza
      - [ ] **Nenhum valor absoluto** em qualquer gráfico (só percentuais ou scores)
      - [ ] A tabela numérica original da Seção 3 continua presente (gráfico é complementar)
      - [ ] Seção 5 com ≤2 alternativas → **V3 não aparece** (pré-requisito 3+ opções)

      ## Critério de falha

      - Falta de V1 ou V2 quando os pré-requisitos estão satisfeitos
      - Caption descritiva ("Gráfico de scores em 5 dimensões.") em vez de interpretativa
      - Qualquer valor em R$/USD absoluto dentro do gráfico
      - Tabela da Seção 3 substituída em vez de complementada
      - V3 aparece com apenas 2 alternativas comparadas
      ```

- [ ] Run: `git grep -l "V1.*radar\|RadarChart" frameworks/ 2>&1; echo "---"; ls frameworks/visualizacao.md 2>&1` — expect: nenhum match em frameworks/, arquivo `frameworks/visualizacao.md` inexistente
- [ ] Commit: `git add tests/07-visualizacao-investimento.md && git commit -m "test(viz): add test 07 — investment happy path V1+V2"`

---

## Task 2: Framework `frameworks/visualizacao.md`

Files: `frameworks/visualizacao.md`

Steps:
- [ ] Write `frameworks/visualizacao.md` com a estrutura exata abaixo (preencher cada seção com o conteúdo correspondente do spec — referência entre parênteses):

      ```markdown
      # Framework — Visualização de dados

      Use sempre que produzir uma análise e o tipo for `investimento` (gatilho automático), ou quando o usuário pedir explicitamente em análises `pessoal` ou `empresarial`. Este framework define o catálogo de gráficos, regras de quando renderizar, e como renderizar por plataforma.

      ## Princípio fundamental

      Visual é **complementar**, nunca substituto. A tabela numérica original da seção correspondente permanece sempre obrigatória. O gráfico se soma para tornar padrões visíveis.

      ## Gatilho (D1 do spec)

      - Tipo `investimento` → renderiza tudo que satisfaça pré-requisito de dados
      - Tipo `pessoal` ou `empresarial` → renderiza apenas se o usuário usar uma das frases-gatilho: "visualize", "mostre o gráfico", "compare graficamente", "gráfico", "diagrama"
      - Tipo `misto` → segue regra do componente investimento se houver; caso contrário opt-in

      ## Catálogo

      ### Catálogo: Radar (V1)

      - **Onde aparece:** dentro da Seção 3, após a tabela de scores
      - **Pré-requisito:** Seção 3 contém 3 ou mais dimensões com score 1-5
      - **Eixos:** as próprias dimensões da tabela (Risco, Retorno esperado, Liquidez, Alinhamento com perfil, Qualidade quando aplicável)
      - **Domínio:** 1 a 5
      - **Cor:** verde-azulado `#0d9488` para a série principal; cinza neutro `#475569` para benchmark/referência se houver

      ### Catálogo: Pizza / donut (V2)

      - **Onde aparece:**
        - Após premissa de composição na Seção 2 quando a alocação é **input** (carteira existente)
        - Dentro da Seção 6 quando a alocação é **output** (recomendação)
      - **Pré-requisito:** dados de alocação com 2 ou mais categorias somando aproximadamente 100% (tolerância ±2pp)
      - **Cor:** paleta determinística (ver abaixo) ordenada por nível de risco crescente

      ### Catálogo: Quadrante risco × retorno (V3)

      - **Onde aparece:** dentro da Seção 5, após a tabela de comparação
      - **Pré-requisito:** Seção 5 compara 3 ou mais opções com risco e retorno qualitativos (mapeáveis para baixo/médio/alto)
      - **Eixos:** X = retorno esperado (baixo→alto), Y = risco (baixo→alto)
      - **Cor:** verde-azulado para opções no quadrante baixo-risco-alto-retorno; âmbar/vermelho conforme risco

      ## Invariantes (D4 do spec)

      1. **Complementar, não substituto** — a tabela numérica da seção original permanece obrigatória
      2. **Sem valores absolutos** — pizza usa %, radar usa score 1-5, quadrante usa qualitativo mapeado. Aplica o protocolo anti-PII do `AGENT.md`.
      3. **Caption obrigatória** — 1-2 linhas em itálico abaixo de cada gráfico dizendo o **insight**, não o eixo. Ver exemplos abaixo.
      4. **Determinismo** — mesma entrada produz mesma cor e mesma ordem de categorias

      ### Caption interpretativa: ✅ vs ❌

      ✅ Bom:
      > _O eixo Liquidez puxa o radar pra baixo — esse é o trade-off que precisa ser aceito conscientemente._

      ❌ Ruim (descritivo):
      > _Gráfico de scores em 5 dimensões._

      ✅ Bom:
      > _Concentração de 60% em renda variável doméstica indica viés de país — o eixo internacional do quadrante mostra o gap._

      ❌ Ruim (genérico):
      > _Pizza mostrando a composição da carteira._

      ## Quando NÃO renderizar (D3 do spec)

      O agente **não** gera o gráfico quando qualquer um for verdadeiro:

      - Pré-requisito de dados não satisfeito (anote a razão na Seção 7)
      - Renderização exigiria expor valor absoluto que viola anti-PII
      - Plataforma corrente não oferece renderização daquele tipo (ver capability matrix abaixo) — neste caso, declarar abertamente: "Visualização V_n_ indisponível nesta plataforma; ver tabela acima."

      ## Paleta determinística

      | Função semântica | Hex |
      |---|---|
      | Favorável / baixo risco | `#0d9488` (verde-azulado) |
      | Neutro / risco médio | `#d97706` (âmbar) |
      | Desfavorável / alto risco | `#dc2626` (vermelho) |
      | Referência / benchmark | `#475569` (cinza neutro) |

      ## Capability matrix por plataforma (D5 do spec)

      | Plataforma | V1 Radar | V2 Pizza | V3 Quadrante |
      |---|---|---|---|
      | Claude (Projects, Cowork) | Artifact React + Recharts `<RadarChart>` | Artifact React + Recharts `<PieChart>` | Artifact React + Recharts `<ScatterChart>` |
      | ChatGPT (Custom GPT com Code Interpreter) | matplotlib `polar` via Code Interpreter | matplotlib `pie` | matplotlib scatter com anotações |
      | Copilot e demais | **Indisponível — declarar** | Mermaid `pie` | Mermaid `quadrantChart` |

      ## Receitas de renderização

      ### Claude — Artifact React (V1 Radar)

      ```jsx
      import { RadarChart, PolarGrid, PolarAngleAxis, PolarRadiusAxis, Radar, ResponsiveContainer } from 'recharts';
      const data = [
        { dim: 'Risco', score: 3 },
        { dim: 'Retorno', score: 4 },
        { dim: 'Liquidez', score: 2 },
        { dim: 'Alinhamento', score: 5 },
      ];
      export default () => (
        <ResponsiveContainer width="100%" height={300}>
          <RadarChart data={data}>
            <PolarGrid /><PolarAngleAxis dataKey="dim" /><PolarRadiusAxis domain={[0,5]} />
            <Radar dataKey="score" stroke="#0d9488" fill="#0d9488" fillOpacity={0.5} />
          </RadarChart>
        </ResponsiveContainer>
      );
      ```

      ### Claude — Artifact React (V2 Pizza)

      ```jsx
      import { PieChart, Pie, Cell, ResponsiveContainer } from 'recharts';
      const data = [
        { name: 'Renda fixa', value: 40, fill: '#0d9488' },
        { name: 'ETF BR', value: 30, fill: '#d97706' },
        { name: 'ETF US', value: 15, fill: '#d97706' },
        { name: 'FII', value: 10, fill: '#d97706' },
        { name: 'Cripto', value: 5, fill: '#dc2626' },
      ];
      export default () => (
        <ResponsiveContainer width="100%" height={300}>
          <PieChart><Pie data={data} dataKey="value" nameKey="name" innerRadius={60} outerRadius={100} label /></PieChart>
        </ResponsiveContainer>
      );
      ```

      ### ChatGPT — matplotlib (V1 Radar)

      ```python
      import matplotlib.pyplot as plt
      import numpy as np
      dims = ['Risco','Retorno','Liquidez','Alinhamento']
      scores = [3, 4, 2, 5]
      angles = np.linspace(0, 2*np.pi, len(dims), endpoint=False).tolist()
      scores += scores[:1]; angles += angles[:1]
      fig, ax = plt.subplots(subplot_kw=dict(polar=True))
      ax.plot(angles, scores, color='#0d9488'); ax.fill(angles, scores, color='#0d9488', alpha=0.5)
      ax.set_xticks(angles[:-1]); ax.set_xticklabels(dims); ax.set_ylim(0,5)
      plt.show()
      ```

      ### Copilot — Mermaid (V2 Pizza)

      ```mermaid
      pie title Composicao
        "Renda fixa" : 40
        "ETF BR" : 30
        "ETF US" : 15
        "FII" : 10
        "Cripto" : 5
      ```

      ### Copilot — Mermaid (V3 Quadrante)

      ```mermaid
      quadrantChart
        title Risco vs Retorno
        x-axis Baixo retorno --> Alto retorno
        y-axis Baixo risco --> Alto risco
        quadrant-1 Alto retorno alto risco
        quadrant-2 Alto retorno baixo risco
        quadrant-3 Baixo retorno baixo risco
        quadrant-4 Baixo retorno alto risco
        Tesouro Selic: [0.2, 0.1]
        ETF BR: [0.55, 0.6]
        Cripto: [0.7, 0.95]
      ```
      ```

- [ ] Run: `Read frameworks/visualizacao.md` — verificar presença de strings-âncora `Catálogo: Radar (V1)`, `Catálogo: Pizza / donut (V2)`, `Catálogo: Quadrante risco × retorno (V3)`, `Paleta determinística`, `Capability matrix`, `Receitas de renderização`
- [ ] Walkthrough: reler `tests/07-visualizacao-investimento.md` e confirmar que cada item ✅ tem uma regra ou exemplo correspondente no novo framework
- [ ] Commit: `git add frameworks/visualizacao.md && git commit -m "feat(viz): add visualization framework with V1/V2/V3 catalog"`

---

## Task 3: Regras de localização no `output-review-ready.md`

Files: `frameworks/output-review-ready.md`

Steps:
- [ ] Edit `frameworks/output-review-ready.md`: na seção `## Regras de preenchimento`, inserir uma nova regra ao final, antes da seção `## Exemplo resolvido`, com este conteúdo exato:

      ```markdown
      - **Visualização (opcional)**: quando aplicável conforme `frameworks/visualizacao.md`, gráficos são inseridos dentro da Seção 3 (radar V1, após a tabela), Seção 5 (quadrante V3, após a tabela) ou Seção 6 / Seção 2 (pizza V2, conforme input vs output). Cada gráfico é seguido por caption interpretativa em itálico. A tabela numérica original **nunca** é substituída pelo gráfico.
      ```

- [ ] Run: `Grep "Visualização (opcional)" frameworks/output-review-ready.md` — expect 1 match
- [ ] Walkthrough: confirmar que a regra remete a `frameworks/visualizacao.md` e cobre o ponto D6 do spec (localização markdown)
- [ ] Commit: `git add frameworks/output-review-ready.md && git commit -m "docs(output): reference visualization placement rules"`

---

## Task 4: Gatilho automático em `investimentos.md`

Files: `frameworks/investimentos.md`

Steps:
- [ ] Edit `frameworks/investimentos.md`: adicionar nova seção `## Gatilho de visualização` após a seção `## Como avaliar uma carteira existente (sem dados pessoais)`, com este conteúdo exato:

      ```markdown
      ## Gatilho de visualização

      Análises de investimento devem renderizar gráficos automaticamente conforme `frameworks/visualizacao.md`. Antes de finalizar a saída, percorra este checklist:

      - [ ] Seção 3 tem 3+ dimensões com score 1-5? → renderizar **V1 (radar)** dentro da Seção 3
      - [ ] Há dados de alocação (input ou recomendação) com 2+ categorias somando ≈100%? → renderizar **V2 (pizza/donut)** no local apropriado (Seção 2 se input, Seção 6 se output)
      - [ ] Seção 5 compara 3+ opções com risco e retorno qualitativos? → renderizar **V3 (quadrante)** dentro da Seção 5

      Quando um pré-requisito não for satisfeito, **não** renderizar aquele gráfico e registrar a razão na Seção 7 (sub-item "Dados ausentes que mudariam a análise" ou "Onde discordo de mim mesmo").

      Quando a plataforma corrente não suportar o tipo de gráfico exigido (ver capability matrix em `frameworks/visualizacao.md`), declarar abertamente em vez de fingir.
      ```

- [ ] Run: `Grep "Gatilho de visualização" frameworks/investimentos.md` — expect 1 match
- [ ] Run: `Grep "V1 (radar)\|V2 (pizza\|V3 (quadrante" frameworks/investimentos.md` — expect 3 matches
- [ ] Walkthrough: reler tests/07 e confirmar que o checklist no framework cobre exatamente as expectativas do test
- [ ] Commit: `git add frameworks/investimentos.md && git commit -m "feat(investimentos): add automatic viz trigger for investment analyses"`

---

## Task 5: Test case 08 — opt-in pessoal/empresarial

Files: `tests/08-visualizacao-opt-in.md`

Steps:
- [ ] Write `tests/08-visualizacao-opt-in.md` com este conteúdo exato:

      ```markdown
      # Teste 08 — Visualização opt-in em análise pessoal/empresarial

      ## Setup

      Carregar `AGENT.md` + frameworks `output-review-ready.md`, `financas-pessoais.md`, `visualizacao.md`.

      Plataforma: Claude Projects.

      Perfil: jurisdição BR, perfil moderado.

      ## Parte A — sem pedido visual

      ### Entrada do usuário

      > Estou com R$ X em dívida no cartão a 12% a.m. e R$ Y em reserva rendendo CDI. Vale antecipar a dívida?

      (R$ X e R$ Y são faixas declaradas conforme protocolo anti-PII; pular se não aplicável.)

      ### Comportamento esperado

      - [ ] As 8 seções padrão presentes
      - [ ] **Nenhum gráfico** na saída (nem V1, nem V2, nem V3)
      - [ ] Seção 7 não menciona ausência de gráfico (não é caso de degradação — é caso de opt-in não acionado)

      ## Parte B — com pedido visual

      ### Entrada do usuário (mesma sessão, na sequência)

      > Mostra graficamente os scores que você atribuiu.

      ### Comportamento esperado

      - [ ] Resposta inclui **V1 (radar)** com as dimensões da Seção 3 da análise anterior
      - [ ] Caption interpretativa em itálico
      - [ ] **V2 e V3 não aparecem** (não foram pedidos e não são gatilho automático em análise pessoal)
      - [ ] A tabela numérica original continua na resposta

      ## Critério de falha

      - Gráfico em Parte A
      - Falta de V1 em Parte B após pedido explícito
      - V2 ou V3 não solicitados aparecendo em Parte B
      - Caption descritiva em vez de interpretativa
      ```

- [ ] Run: `Grep -l "opt-in\|gatilho" frameworks/financas-pessoais.md frameworks/financas-empresariais.md 2>&1` — expect: nenhum match (failure red)
- [ ] Commit: `git add tests/08-visualizacao-opt-in.md && git commit -m "test(viz): add test 08 — opt-in for personal/business analyses"`

---

## Task 6: Opt-in em `financas-pessoais.md` e `financas-empresariais.md`

Files: `frameworks/financas-pessoais.md`, `frameworks/financas-empresariais.md`

Steps:
- [ ] Edit `frameworks/financas-pessoais.md`: ao final do arquivo, adicionar esta seção exatamente:

      ```markdown
      ## Gatilho de visualização (opt-in)

      Análises pessoais **não** renderizam gráficos por padrão — o output é principalmente narrativo e tabular. Renderizar conforme `frameworks/visualizacao.md` **apenas** quando o usuário pedir explicitamente. Frases-gatilho reconhecidas:

      - "visualize"
      - "mostre o gráfico"
      - "compare graficamente"
      - "gráfico"
      - "diagrama"

      Quando o gatilho for acionado, renderizar somente os gráficos cujos pré-requisitos de dados estão satisfeitos. Não inferir o que o usuário "deveria" querer ver — render apenas o que está expresso.
      ```

- [ ] Edit `frameworks/financas-empresariais.md`: ao final do arquivo, adicionar **a mesma seção** com texto idêntico (substituir "pessoais" por "empresariais" na primeira linha)
- [ ] Run: `Grep -c "Gatilho de visualização (opt-in)" frameworks/financas-pessoais.md frameworks/financas-empresariais.md` — expect 1 match em cada arquivo
- [ ] Walkthrough: reler tests/08 Parte A e B e confirmar que as regras do framework produzem o comportamento esperado
- [ ] Commit: `git add frameworks/financas-pessoais.md frameworks/financas-empresariais.md && git commit -m "feat(personal,business): add explicit opt-in for visualization"`

---

## Task 7: Test case 09 — degradação no Copilot

Files: `tests/09-visualizacao-degradacao.md`

Steps:
- [ ] Write `tests/09-visualizacao-degradacao.md` com este conteúdo exato:

      ```markdown
      # Teste 09 — Degradação de visualização por plataforma

      ## Setup

      Carregar `AGENT.md` + `frameworks/output-review-ready.md` + `frameworks/investimentos.md` + `frameworks/visualizacao.md`.

      Plataforma: Copilot (ou simular — informar ao agente "estou no Copilot, sem Artifacts nem Code Interpreter").

      Perfil: moderado, jurisdição BR, horizonte 10+.

      ## Entrada do usuário

      > Quero comparar três opções para minha parcela de renda fixa: Tesouro Selic, CDB liquidez diária, e fundo DI. Avalie e mostre graficamente.

      ## Comportamento esperado

      - [ ] As 8 seções padrão presentes
      - [ ] Seção 5 tem 3 opções comparadas (preenche pré-requisito de V3)
      - [ ] **V3 (quadrante)** aparece como bloco Mermaid `quadrantChart` (Copilot suporta Mermaid)
      - [ ] Se Seção 3 tiver 3+ dimensões, agente declara: "Visualização V1 indisponível nesta plataforma; ver tabela acima." dentro da Seção 3 ou Seção 7
      - [ ] **Nenhum** bloco React/JSX/Recharts na resposta (incompatível com Copilot)
      - [ ] Caption interpretativa abaixo do quadrante

      ## Critério de falha

      - Bloco React/JSX presente
      - Ausência de declaração explícita sobre indisponibilidade do radar
      - Mermaid mal-formado (sintaxe quebrada)
      - Caption descritiva
      ```

- [ ] Run: `Grep -l "Copilot\|capability" guias-instalacao/copilot.md` — expect: arquivo existe mas não tem "capability matrix" ainda
- [ ] Run: `Grep "Visualização V1 indisponível" frameworks/ guias-instalacao/` — expect 0 matches (failure red)
- [ ] Commit: `git add tests/09-visualizacao-degradacao.md && git commit -m "test(viz): add test 09 — Copilot platform degradation"`

---

## Task 8: Capability declarations nos 3 guias de instalação

Files: `guias-instalacao/claude.md`, `guias-instalacao/chatgpt.md`, `guias-instalacao/copilot.md`

Steps:
- [ ] Edit `guias-instalacao/claude.md`: substituir a seção `## Limitações nesta plataforma` por:

      ```markdown
      ## Capacidades de visualização

      O Claude Projects oferece **Artifacts**, que renderizam React + Recharts inline. Os três gráficos do catálogo de `frameworks/visualizacao.md` (V1 radar, V2 pizza, V3 quadrante) funcionam todos. Use Artifacts independentes ou um único Artifact agrupando múltiplos gráficos da mesma análise.

      ## Limitações nesta plataforma

      - O Claude pode hesitar em executar escrita automática em alguns ambientes. Se ele apenas mostrar o conteúdo proposto, copie e salve manualmente
      - O tamanho total dos arquivos do Project tem limite — o ralph-loop é essencial para manter os arquivos pequenos
      - Artifacts têm limite de tamanho (kilobytes); para análises com muitos gráficos, dividir em Artifacts separados
      ```

- [ ] Edit `guias-instalacao/chatgpt.md`: adicionar (ou substituir a seção de limitações por) uma nova seção `## Capacidades de visualização`:

      ```markdown
      ## Capacidades de visualização

      Os gráficos do catálogo `frameworks/visualizacao.md` dependem do **Code Interpreter** (também chamado de "Advanced Data Analysis"). Habilite-o nas configurações do Custom GPT.

      Com Code Interpreter ligado:

      - V1 (radar), V2 (pizza), V3 (quadrante) renderizam como PNG inline via matplotlib
      - Use a paleta definida no framework

      Sem Code Interpreter, o agente deve declarar: "Visualização V_n_ indisponível nesta sessão (Code Interpreter desligado); ver tabela acima." e seguir só com tabelas.
      ```

- [ ] Edit `guias-instalacao/copilot.md`: adicionar (ou substituir seção de limitações por) uma nova seção `## Capacidades de visualização`:

      ```markdown
      ## Capacidades de visualização

      O Copilot não tem Artifacts nem Code Interpreter para renderização. Resultado:

      | Gráfico | Suporte no Copilot |
      |---|---|
      | V1 (radar) | **Indisponível** — agente declara: "Visualização V1 indisponível nesta plataforma; ver tabela acima." |
      | V2 (pizza) | **Mermaid `pie`** — funciona em renderizadores compatíveis (GitHub, alguns clients) |
      | V3 (quadrante) | **Mermaid `quadrantChart`** — idem |

      Em ambientes onde o cliente do usuário não renderiza Mermaid, o bloco aparece como texto bruto. Isso é aceito: o conteúdo da análise (tabelas + texto) já é completo.
      ```

- [ ] Run: `Grep "Capacidades de visualização" guias-instalacao/claude.md guias-instalacao/chatgpt.md guias-instalacao/copilot.md` — expect 1 match em cada arquivo
- [ ] Run: `Grep "Visualização V1 indisponível" guias-instalacao/copilot.md guias-instalacao/chatgpt.md` — expect ≥1 match
- [ ] Walkthrough: reler tests/09 e confirmar que o guia do Copilot agora documenta o comportamento esperado
- [ ] Commit: `git add guias-instalacao/claude.md guias-instalacao/chatgpt.md guias-instalacao/copilot.md && git commit -m "docs(install): document visualization capabilities per platform"`

---

## Task 9: Referência no `AGENT.md`

Files: `AGENT.md`

Steps:
- [ ] Edit `AGENT.md`: adicionar nova seção `## Protocolo de visualização` imediatamente após `## Sinalização proativa de desatualização` e antes de `## Protocolo de fim de sessão`, com este conteúdo exato:

      ```markdown
      ## Protocolo de visualização

      Quando a análise é do tipo `investimento` ou quando o usuário pediu explicitamente em análise `pessoal`/`empresarial`, consulte `frameworks/visualizacao.md` para o catálogo de gráficos (V1 radar, V2 pizza, V3 quadrante), os pré-requisitos de dados, a paleta determinística, e as receitas de renderização por plataforma.

      Princípios não-negociáveis:

      1. Visual é **complementar** à tabela numérica, nunca substituto
      2. **Nenhum valor absoluto** em qualquer gráfico (mantém o protocolo anti-dados-sensíveis)
      3. Cada gráfico é seguido de **caption interpretativa** em itálico — descreve o insight, não o eixo
      4. Quando a plataforma não suporta o tipo de gráfico necessário, declarar abertamente em vez de fingir

      Se nenhum pré-requisito de dados estiver satisfeito, não renderizar nada — registrar a razão na Seção 7 do output review-ready.
      ```

- [ ] Run: `Grep "Protocolo de visualização" AGENT.md` — expect 1 match
- [ ] Run: `Grep "frameworks/visualizacao.md" AGENT.md` — expect 1 match
- [ ] Walkthrough: ler AGENT.md inteiro de cima a baixo e confirmar fluxo coerente entre as seções
- [ ] Commit: `git add AGENT.md && git commit -m "feat(agent): add visualization protocol referencing viz framework"`

---

## Task 10: Bump VERSAO + CHANGELOG + atualização tests/README

Files: `VERSAO.md`, `CHANGELOG.md`, `tests/README.md`

Steps:
- [ ] Edit `VERSAO.md`: trocar `0.1.0` por `0.2.0` na linha "Versão da skill"
- [ ] Edit `CHANGELOG.md`: na seção `## [Unreleased]`, inserir abaixo dela uma nova seção:

      ```markdown
      ## [0.2.0] — 2026-05-15

      ### Added
      - Framework `frameworks/visualizacao.md` com catálogo V1 (radar), V2 (pizza), V3 (quadrante)
      - Gatilho automático de visualização em análises de investimento (`frameworks/investimentos.md`)
      - Opt-in explícito de visualização em análises pessoais e empresariais
      - Capability matrix de renderização por plataforma (Claude / ChatGPT / Copilot)
      - Test cases 07, 08, 09 cobrindo happy path, opt-in e degradação
      - Protocolo de visualização no `AGENT.md`

      ### Changed
      - `frameworks/output-review-ready.md` referencia regras de localização dos visuais
      - Guias de instalação documentam capacidades de visualização por plataforma
      ```

- [ ] Edit `tests/README.md`: adicionar à `## Lista de testes`:

      ```
      - `07-visualizacao-investimento.md` — visualização padrão em análise de investimento (Claude)
      - `08-visualizacao-opt-in.md` — opt-in em análises pessoal/empresarial
      - `09-visualizacao-degradacao.md` — degradação em plataformas sem Artifacts/Code Interpreter
      ```

      e atualizar o `## Critério geral de release`:

      ```markdown
      ## Critério geral de release

      A v0.2.0 só pode ser tagueada quando os 9 testes passarem.
      ```

- [ ] Run: `Grep "0.2.0" VERSAO.md CHANGELOG.md tests/README.md` — expect ≥3 matches
- [ ] Run: `Grep "07-visualizacao\|08-visualizacao\|09-visualizacao" tests/README.md` — expect 3 matches
- [ ] Commit: `git add VERSAO.md CHANGELOG.md tests/README.md && git commit -m "chore: release 0.2.0 — visualization layer"`

---

## Task 11: Push e tag

Files: nenhum (operação git)

Steps:
- [ ] Run: `git log --oneline -12` — confirmar que os 10 commits anteriores estão em ordem
- [ ] Run: `git push origin main`
- [ ] Run: `git tag -a v0.2.0 -m "v0.2.0 — visualization layer"`
- [ ] Run: `git push origin v0.2.0`
- [ ] Run: `gh release create v0.2.0 --title "v0.2.0 — Visualization layer" --notes-from-tag` (opcional, se o usuário quiser release pública)

---

## Self-review checklist

- [x] Cobre D1 (gatilho) — Tasks 4, 6
- [x] Cobre D2 (catálogo) — Task 2
- [x] Cobre D3 (não-renderização) — Task 2 (framework) + Task 4 (integração com investimentos)
- [x] Cobre D4 (invariantes) — Task 2 + Task 9 (princípios no AGENT.md)
- [x] Cobre D5 (capability matrix) — Task 2 (framework) + Task 8 (guias)
- [x] Cobre D6 (localização markdown) — Task 3
- [x] Todos os 12 arquivos do spec aparecem como files em alguma task
- [x] Adicionalmente: `tests/README.md` (Task 10) — não estava no spec mas precisa atualizar
- [x] Sem placeholder language ("TBD", "implementar adequadamente")
- [x] Cada task tem conteúdo concreto ou Edit com texto exato
- [x] Cada task tem verificação (Grep ou Read específico) e commit message
- [x] Tasks dimensionadas para 2-5 min cada (algumas — Task 2 e Task 8 — podem ir até 10 min por volume de texto, ainda aceitável)
- [x] Ordem respeita dependências: framework antes do guia que o referencia, test antes do framework que satisfaz o test

## Riscos de execução

- **Conteúdo do `frameworks/visualizacao.md` é volumoso (Task 2):** o executor deve referenciar o spec D2-D5 enquanto escreve, não inventar conteúdo
- **Sintaxe Mermaid `quadrantChart`:** verificar versão suportada pelo Mermaid corrente; a sintaxe é estável desde 10.x
- **Caption interpretativa é qualitativo:** difícil de mecanizar — o walkthrough humano é o gate final
