# Spec — Visualização de dados nas análises

**Data:** 2026-05-15
**Status:** Design aprovado, pendente plano de implementação
**Autor:** brainstorming session com o mantenedor

## Motivação

O output review-ready atual é denso em texto e tabelas. Para análises de investimento, padrões importantes (desbalanceamento de scores, concentração de carteira, dispersão risco × retorno) ficam escondidos na tabela. Uma camada de visualização aumenta a velocidade de leitura tanto pelo usuário quanto pelo revisor humano subsequente, sem mudar o conteúdo analítico.

## Escopo

Este spec cobre **apenas visualização de dados** — gráficos derivados de dimensões quantificadas que o agente já produz. Está fora de escopo: diagramas conceituais (fluxograma de decisão, árvore de teses), identidade visual (ícones, badges), e imagens ilustrativas.

## Decisões de design

### D1. Gatilho

- **Análises de tipo `investimento`** → visualização padrão, sempre que os dados de saída suportarem o gráfico (regra D3)
- **Análises `pessoal` e `empresarial`** → opt-in explícito do usuário. Frases-gatilho: "visualize", "mostre o gráfico", "compare graficamente", "gráfico", "diagrama" (todos no contexto da análise corrente)
- **Análises `misto`** → segue a regra do componente investimento se houver; caso contrário, opt-in

**Justificativa:** análises de investimento têm naturalmente dimensões quantificáveis comparáveis (risco, retorno, liquidez, alocação). Pessoal e empresarial frequentemente são qualitativas ou narrativas, onde gráfico vira poluição.

### D2. Catálogo

Três tipos de gráfico:

| ID | Tipo | Onde aparece | Pré-requisito de dados |
|---|---|---|---|
| V1 | Radar de scores | dentro da Seção 3 (após a tabela) | Seção 3 tem 3+ dimensões com score 1-5 |
| V2 | Pizza / donut de alocação | dentro da Seção 6, ou imediatamente após premissa de composição na Seção 2 | dados de alocação (classes de ativo, ativos individuais, ou geografia) com 2+ categorias somando ≈100% |
| V3 | Quadrante risco × retorno | dentro da Seção 5 | Seção 5 compara 3+ opções com risco e retorno qualitativos mapeáveis para eixo |

**Não inclui:** barras comparativas (V não selecionado no brainstorm; cobertura redundante com a tabela da Seção 5).

### D3. Regras de não-renderização

O agente **não** gera o gráfico quando:

- Pré-requisito de dados não é satisfeito (ex: Seção 5 tem 2 opções → não há quadrante)
- Renderização exigiria expor valor absoluto que viola o protocolo anti-PII (ex: pizza com valores em reais nominais; o correto é percentual)
- Plataforma corrente não tem renderização disponível para aquele tipo (ver D5) — neste caso, declara isso na resposta e segue só com a tabela

Quando o agente não gera o gráfico esperado, deve registrar o motivo na Seção 7 (Para o revisor) — sub-item "Onde discordo de mim mesmo" ou "Dados ausentes que mudariam a análise".

### D4. Invariantes do visual

- **Complementar, não substituto:** a tabela numérica original na Seção 3/5 continua obrigatória. O gráfico se soma, não substitui.
- **Sem valores absolutos:** mantém o protocolo anti-PII. Pizza usa %; radar usa score 1-5; quadrante usa qualitativo (baixo/médio/alto) mapeado para coordenada.
- **Caption obrigatório:** cada gráfico vem com 1-2 linhas em itálico abaixo dizendo o **insight** (não o quê — isso o eixo já diz). Ex: ✅ "_O eixo Liquidez puxa o radar pra baixo — esse é o trade-off que precisa ser aceito conscientemente_." Não: "_Gráfico de scores em 5 dimensões._"
- **Determinismo:** mesma input → mesma cor / mesma ordem de categorias. Investimentos de menor risco aparecem em verde-azulado; maior risco em âmbar-vermelho.

### D5. Renderização por plataforma (best-in-class)

| Plataforma | V1 Radar | V2 Pizza | V3 Quadrante |
|---|---|---|---|
| **Claude** (Projects, Cowork) | Artifact React + Recharts `<RadarChart>` | Artifact React + Recharts `<PieChart>` ou `<RadialBarChart>` | Artifact React + Recharts `<ScatterChart>` com eixos qualitativos |
| **ChatGPT** (Custom GPT / Plus) | Code Interpreter / `python_user_visible` com matplotlib `polar` | matplotlib `pie` | matplotlib scatter com anotações |
| **Copilot** e demais | Não disponível — declarar e seguir com tabela | Mermaid `pie` | Mermaid `quadrantChart` |

**Paleta determinística** (definida no framework, igual entre plataformas):

- Verde-azulado `#0d9488` — favorável / baixo risco
- Âmbar `#d97706` — neutro / risco médio
- Vermelho `#dc2626` — desfavorável / alto risco
- Cinza neutro `#475569` — referência / benchmark

### D6. Localização exata no markdown de saída

```markdown
## 3. Análise estruturada

| Dimensão | Score (1-5) | Justificativa breve |
|---|---|---|
| ... | ... | ... |

<!-- V1: radar de scores aqui -->

_<caption do radar — 1-2 linhas explicando o insight>_
```

E para V3:

```markdown
## 5. Comparação com alternativas

| Opção | Quando faz sentido | Trade-off vs tese atual |
|---|---|---|
| ... | ... | ... |

<!-- V3: quadrante risco × retorno aqui -->

_<caption do quadrante>_
```

V2 (pizza) pode aparecer em dois lugares dependendo do uso:
- Se a alocação é **input** (carteira existente do usuário) → após premissa correspondente na Seção 2
- Se a alocação é **output** (recomendação de composição) → dentro da Seção 6, após a recomendação correspondente

## Mudanças no repositório

| Arquivo | Tipo | Conteúdo |
|---|---|---|
| `frameworks/visualizacao.md` | novo | Catálogo (V1/V2/V3), invariantes, regras de não-renderização, paleta, exemplos de código por plataforma |
| `frameworks/output-review-ready.md` | edição | Adicionar regra de localização dos gráficos (D6) e remetente ao framework de visualização |
| `frameworks/investimentos.md` | edição | Gatilho automático para análises de investimento; checklist de "tem dado para V1/V2/V3?" |
| `frameworks/financas-pessoais.md` | edição | Documentar opt-in explícito |
| `frameworks/financas-empresariais.md` | edição | Documentar opt-in explícito |
| `AGENT.md` | edição | Adicionar visualização como protocolo opcional; remetente a `frameworks/visualizacao.md` |
| `guias-instalacao/claude.md` | edição | Como o Artifact é invocado, limites de tamanho |
| `guias-instalacao/chatgpt.md` | edição | Necessidade de Code Interpreter habilitado; comportamento sem ele |
| `guias-instalacao/copilot.md` | edição | Limites (sem radar); declaração obrigatória de degradação |
| `tests/07-visualizacao-investimento.md` | novo | Caso de teste: análise de investimento com dados suficientes; agente deve gerar V1+V2; revisar caption |
| `tests/08-visualizacao-opt-in.md` | novo | Caso de teste: análise pessoal sem pedido (não gera); com pedido (gera só V aplicável) |
| `tests/09-visualizacao-degradacao.md` | novo | Caso de teste: simular execução em Copilot; agente declara não-disponibilidade do radar e segue |
| `VERSAO.md` | edição | Bump para v0.2.0 |
| `CHANGELOG.md` | edição | Entrada da feature |

## Riscos e mitigações

| Risco | Mitigação |
|---|---|
| Agente gera gráfico em situação onde o dado é insuficiente, criando ilusão de precisão | Pré-requisitos D3 explícitos no framework; teste 08 cobre o caso |
| Caption descritiva em vez de interpretativa, virando ruído | Exemplos explícitos em `visualizacao.md` mostrando ✅ vs ❌; regra de revisão na seção 7 do output |
| Visual expõe dado sensível por engano (pizza com R$ absoluto) | Invariante D4 reiterada no framework anti-PII e em cada exemplo do catálogo |
| Plataforma muda capabilities (ex: ChatGPT desabilita Code Interpreter) | Caminho de degradação D5 é explícito; agente declara abertamente em vez de fingir |
| Inflação visual em análises simples polui o output | Pré-requisitos D3 e regra D1 (opt-in para pessoal/empresarial) limitam |

## Critérios de aceitação

1. Análise de investimento com 5 dimensões em Seção 3, sob Claude → output contém V1 (radar via Artifact) com caption interpretativa
2. Mesma análise sob ChatGPT com Code Interpreter → V1 vem como PNG matplotlib inline
3. Mesma análise sob Copilot → output não contém V1 e declara a degradação na Seção 7
4. Análise pessoal sem pedido visual → output sem gráficos
5. Análise pessoal com "mostre o gráfico" no pedido → output contém V aplicável; demais não aparecem
6. Análise com 2 alternativas → V3 não aparece (pré-requisito 3+)
7. Análise com pizza pedida em valores absolutos → agente recusa, sugere percentuais
8. Caption de teste 1 é interpretativa (não puramente descritiva) — verificado em test case 07

## Fora de escopo (para futuras specs)

- Diagramas conceituais (fluxograma de decisão de tese, árvore causa-efeito)
- Identidade visual (ícones de seção, badges)
- Imagens ilustrativas geradas
- Gráficos temporais (histórico de retorno) — exige dado de mercado em tempo real, que o agente não tem
- V4 barras comparativas — descartado neste round
