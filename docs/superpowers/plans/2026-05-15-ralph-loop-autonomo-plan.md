# Plano — Ralph-loop autônomo (v0.3.0)

**Feature:** Escrita silenciosa autônoma de estado em plataformas com acesso a arquivos (primário: Claude Code/Cowork)
**Goal:** Implementar D1-D6 do spec aprovado em `docs/superpowers/specs/2026-05-15-ralph-loop-autonomo-design.md`, bumpar versão 0.2.0 → 0.3.0
**Spec referência:** `docs/superpowers/specs/2026-05-15-ralph-loop-autonomo-design.md` — toda decisão deve ser rastreável a D1-D6
**Pré-requisito:** spec lido na íntegra antes da Task 1

## Arquitetura

| Arquivo | Tipo | Responsabilidade única |
|---|---|---|
| `tests/10-ralph-autonomo.md` | novo | Test case: escrita silenciosa, .bak, revert, resumo final, modo Projects sem regressão |
| `tests/11-compressao-determinista.md` | novo | Test case: D4.1 invalidação, D4.2 TTL, D4.3 pressão, D4.4 limite 2 KB |
| `frameworks/ralph-loop.md` | novo | Protocolo autônomo: D2 eventos, D3 escrita+revert, D5 resumo, D6 detecção de modo |
| `frameworks/compressao-estado.md` | novo | Regras D4.1-D4.4 com exemplos e formato de "Lições aprendidas" |
| `AGENT.md` | edição | Substituir `## Protocolo de fim de sessão` por referência aos dois novos frameworks |
| `estado-agente/perfil.template.md` | edição | Adicionar bloco `## Lições aprendidas (arquivado)` vazio |
| `estado-agente/situacao-relativa.template.md` | edição | Idem |
| `estado-agente/teses-ativas.template.md` | edição | Idem |
| `estado-agente/decisoes.template.md` | edição | Idem |
| `guias-instalacao/claude.md` | edição | Documentar modo Cowork autônomo, `.bak` e revert |
| `guias-instalacao/chatgpt.md` | edição | Declarar modo manual (autônomo é Cowork-only) |
| `guias-instalacao/copilot.md` | edição | Idem |
| `tests/README.md` | edição | Adicionar 10/11; release condicionada à v0.3.0 |
| `VERSAO.md` | edição | Bump 0.2.0 → 0.3.0 |
| `CHANGELOG.md` | edição | Entrada [0.3.0] |

## Stack

- Markdown puro (CommonMark/GFM)
- Convenção `<arquivo>.md.bak` (sufixo simples — 1 nível de revert)
- Verificação: `Grep` para strings-âncora, `Read` para validação de estrutura, walkthrough manual contra os test cases

## Convenção TDD adaptada

Como o projeto é um agente definido por frameworks markdown (sem código executável), cada ciclo é:

1. **Red:** escrever ou atualizar o test case com expectativas verificáveis por grep em frameworks/guias
2. **Confirma Red:** grep nas strings-âncora → 0 matches
3. **Green:** criar/editar o framework com a string-âncora e o conteúdo de fato
4. **Confirma Green:** mesmo grep → ≥1 match
5. **Walkthrough:** ler framework + test case lado a lado, confirmar que um humano executando o test marcaria ✅
6. **Commit**

---

## Task 1: Test case 10 — ralph-loop autônomo

Files: `tests/10-ralph-autonomo.md`

Steps:
- [ ] Write `tests/10-ralph-autonomo.md` com este conteúdo exato:

      ```markdown
      # Teste 10 — Ralph-loop autônomo (modo Cowork)

      ## Setup

      Plataforma: Claude Code ou Cowork com `Write`/`Edit` disponíveis.

      Carregar `AGENT.md` + `frameworks/ralph-loop.md` + `frameworks/compressao-estado.md` + todos os frameworks padrão.

      Estado inicial em `estado-agente/`:

      - `perfil.md`: tolerância **moderada**, jurisdição **BR**, horizonte 10+
      - `teses-ativas.md`: 2 teses ativas T1, T2
      - `situacao-relativa.md`, `decisoes.md`: preenchidos com 1 item cada

      Nenhum `.bak` existe antes da sessão.

      ## Parte A — silêncio durante escrita

      ### Entrada do usuário

      > Atualizei minha tolerância a risco: agora estou arrojado (passei a aceitar drawdown maior).

      ### Comportamento esperado

      - [ ] Agente atualiza `estado-agente/perfil.md` (campo "Tolerância a risco declarada" → arrojado)
      - [ ] Antes do write, cria `estado-agente/perfil.md.bak` com o conteúdo anterior
      - [ ] Resposta no chat **não menciona** a escrita (sem "salvei perfil.md")
      - [ ] Outros 3 arquivos (situacao-relativa, teses-ativas, decisoes) não são alterados

      ## Parte B — não-evento não dispara escrita

      ### Entrada do usuário (mesma sessão)

      > E se eu colocasse 80% em cripto? Só pensando hipoteticamente.

      ### Comportamento esperado

      - [ ] Agente responde à hipótese (análise normal)
      - [ ] **Nenhum** arquivo de estado é modificado (especulação não é evento — D2 do spec)
      - [ ] Nenhum `.bak` adicional é criado

      ## Parte C — resumo de encerramento

      ### Entrada do usuário

      > Obrigado, vou encerrar por agora.

      ### Comportamento esperado

      - [ ] Resposta inclui bloco "## Estado atualizado nesta sessão"
      - [ ] Lista exatamente a mudança da Parte A (`perfil.md: tolerância moderada → arrojada`)
      - [ ] Menciona que backup foi salvo e como reverter
      - [ ] **Não** lista falsos updates da Parte B

      ## Parte D — revert seletivo

      ### Entrada do usuário (mesma sessão, depois do resumo)

      > reverta perfil

      ### Comportamento esperado

      - [ ] `estado-agente/perfil.md` volta ao conteúdo anterior (tolerância moderada)
      - [ ] `.bak` permanece (não é deletado pelo revert; só sobrescrito por nova escrita)
      - [ ] Confirmação curta no chat: "Restaurei: perfil.md"

      ## Parte E — modo Projects sem regressão

      ### Setup adicional

      Simular ambiente sem Write/Edit: declarar ao agente "estou no Claude Projects, sem ferramentas de escrita".

      ### Entrada do usuário

      > Atualizei meu horizonte para 5-10 anos.

      ### Comportamento esperado

      - [ ] Agente **não** tenta escrever arquivo
      - [ ] Acumula a mudança internamente
      - [ ] No fim da sessão (despedida), agente **propõe** a reescrita no chat (modelo manual atual), sem inventar capacidade
      - [ ] Nenhuma mensagem do tipo "Write tool não disponível" durante a sessão (silêncio também nesse modo)

      ## Critério de falha

      - Mensagem de "salvei <arquivo>" no chat durante Partes A ou B
      - Escrita disparada na Parte B (especulação)
      - Resumo da Parte C sem listar mudança da A, ou listando algo da B
      - Revert da Parte D que falha ou afeta arquivo não pedido
      - Parte E: agente tenta usar Write quando ferramenta não existe
      ```

- [ ] Run: verificar que strings-âncora ainda não existem em frameworks:
      `Grep "Protocolo de visualização|frameworks/ralph-loop.md|frameworks/compressao-estado.md" frameworks/ AGENT.md` — expect: apenas o match existente de `Protocolo de visualização` em AGENT.md (de v0.2.0); zero matches dos dois caminhos novos
- [ ] Commit: `git add tests/10-ralph-autonomo.md && git commit -m "test(ralph): add test 10 — autonomous loop, silent write, bak, revert, Projects degradation"`

---

## Task 2: Test case 11 — compressão determinística

Files: `tests/11-compressao-determinista.md`

Steps:
- [ ] Write `tests/11-compressao-determinista.md` com este conteúdo exato:

      ```markdown
      # Teste 11 — Compressão determinística do estado

      ## Setup comum

      Plataforma: Claude Code com Write/Edit.

      Carregar `AGENT.md` + `frameworks/ralph-loop.md` + `frameworks/compressao-estado.md`.

      ## Parte A — D4.1 invalidação imediata

      ### Setup A

      `teses-ativas.md` contém:

      ```
      ## Teses ativas

      - [T1, 2026-03-15] Small caps BR concentradas — convicção alta, motivada por ciclo de juros caindo
      - [T2, 2026-04-10] FII logístico segmento Y — convicção média
      ```

      ### Entrada do usuário

      > A premissa principal da T1 caiu: o ciclo de juros foi revertido. A tese não vale mais.

      ### Comportamento esperado A

      - [ ] T1 é movida imediatamente do bloco "Teses ativas" para `## Lições aprendidas (arquivado)`
      - [ ] Linha condensada segue o formato: `- [T1, 2026-03-15→<data atual>] Small caps BR — invalidada quando ciclo de juros foi revertido; lição: dependência de regime macro.`
      - [ ] T2 permanece intacta em "Teses ativas"
      - [ ] Update silencioso (sem mensagem no chat sobre o move; só aparece no resumo de encerramento)

      ## Parte B — D4.2 TTL > 90 dias sem referência

      ### Setup B

      `decisoes.md` contém 4 decisões, criadas em janeiro/fevereiro de 2026, nenhuma referenciada em análises dos últimos 30 dias. Data atual da sessão: 2026-05-15.

      ### Entrada do usuário

      > Vamos rever a alocação geral.

      ### Comportamento esperado B

      - [ ] Durante a sessão, agente não move nada (TTL é varredura de fim de sessão)
      - [ ] Ao detectar fim de sessão, agente move as 4 decisões para "Lições aprendidas (arquivado)" em **uma linha agregada por categoria/mês**, ex: `- [D1, D2, D3, D4 — jan-fev 2026] Rebalanceamentos triviais — todos mantidos conforme esperado.`
      - [ ] Bloco "Ativos" de `decisoes.md` fica vazio (ou só com o template "Nenhuma decisão ativa")

      ## Parte C — D4.3 pressão de tamanho

      ### Setup C

      `teses-ativas.md` está com 1.45 KB (5 teses ativas detalhadas).

      ### Entrada do usuário

      > Adiciona uma nova tese: ETF de tecnologia EUA, médio prazo, convicção alta porque acredito em IA.

      ### Comportamento esperado C

      - [ ] Agente adiciona a nova tese T6
      - [ ] Após o write, arquivo passaria de 1.5 KB → trigger D4.3
      - [ ] Agente varre o item mais antigo do bloco "Teses ativas" (data de última referência mais antiga) e move para "Lições aprendidas"
      - [ ] Arquivo final fica < 1.2 KB
      - [ ] `wc -c estado-agente/teses-ativas.md` confirma < 1228 bytes

      ## Parte D — D4.4 limite invariante 2 KB

      ### Setup D

      `teses-ativas.md` está com 1.95 KB, todos os itens criados nos últimos 30 dias (não passíveis de TTL nem de varredura por idade).

      ### Entrada do usuário

      > Adiciona mais uma tese: cripto, alta convicção.

      ### Comportamento esperado D

      - [ ] Agente tenta adicionar T7
      - [ ] Arquivo passaria de 2 KB
      - [ ] D4.3 tenta varrer mas todos os itens são recentes → não consegue reduzir
      - [ ] Agente **não** persiste a nova tese
      - [ ] Mensagem visível no chat (única quebra de silêncio): "teses-ativas.md atingiu o limite de 2 KB — preciso de instrução sua para reorganizar antes de adicionar T7."

      ## Como medir

      Em Code:

      ```bash
      wc -c estado-agente/*.md
      ```

      Cada arquivo deve ser ≤ 2048 bytes em todos os momentos do teste, exceto pela tentativa proibida da Parte D.

      ## Critério de falha

      - Parte A: T1 ainda no bloco "Teses ativas" depois da invalidação
      - Parte B: D4.2 não roda no fim de sessão, ou move decisões linha-por-linha em vez de agregadas
      - Parte C: arquivo > 1.2 KB depois de varredura
      - Parte D: arquivo > 2 KB persistido em disco; ou agente não declara o limite
      ```

- [ ] Run: `Grep "compressao-estado\|D4.1\|D4.2\|D4.3\|D4.4" frameworks/` — expect: 0 matches (red)
- [ ] Commit: `git add tests/11-compressao-determinista.md && git commit -m "test(ralph): add test 11 — deterministic compression rules"`

---

## Task 3: Framework `frameworks/ralph-loop.md`

Files: `frameworks/ralph-loop.md`

Steps:
- [ ] Write `frameworks/ralph-loop.md` com este conteúdo exato:

      ```markdown
      # Framework — Ralph-loop autônomo

      Define como o agente persiste estado (`estado-agente/*.md`) sem ruído no chat. Roda em modo **autônomo** quando ferramentas de escrita estão disponíveis (Claude Code, Cowork); roda em modo **manual** caso contrário (Claude Projects, ChatGPT, Copilot).

      ## Detecção de modo (D6 do spec)

      No início de cada sessão, durante o protocolo de leitura de estado (`AGENT.md`):

      - Se houver ferramentas `Write` ou `Edit` disponíveis → **modo autônomo**
      - Caso contrário → **modo manual** (comportamento pré-v0.3)

      Não declarar o modo no chat. Só explicar se o usuário perguntar diretamente como o estado é salvo.

      ## Eventos que disparam update (D2 do spec)

      O agente escreve em um arquivo específico quando uma das condições abaixo é satisfeita:

      ### Eventos de `estado-agente/perfil.md`

      Usuário declara novo valor para qualquer campo do perfil:

      - Faixa etária
      - Jurisdição fiscal
      - Dependentes
      - Estabilidade da renda
      - Tolerância a risco declarada
      - Horizonte principal
      - Objetivos rotulados
      - Restrições/preferências

      ### Eventos de `estado-agente/situacao-relativa.md`

      Usuário declara nova informação financeira em faixas ou percentuais (não valores absolutos):

      - Proporção dívida/renda
      - Cobertura da reserva em meses
      - Distribuição percentual entre classes de ativo
      - Faixa de patrimônio relativa a meta

      ### Eventos de `estado-agente/teses-ativas.md`

      - Análise produz nova tese explícita
      - Tese existente é referenciada com nova evidência (refina, fortalece ou desafia)
      - Usuário relata desfecho de uma tese (validação ou invalidação)

      ### Eventos de `estado-agente/decisoes.md`

      - Usuário declara que aplicou uma recomendação ("vou seguir", "comprei", "rejeitei")
      - Usuário declara um desfecho de decisão anterior

      ## Não-eventos (não disparam write)

      - Pergunta exploratória sem declaração de fato persistente ("e se...?", "vale a pena?")
      - Análise produzida pelo agente sem evidência de adoção
      - Especulação ou "what if"
      - Pedido de explicação conceitual
      - Conversação social (cumprimento, agradecimento)

      Em dúvida entre evento e não-evento, **não escrever**. O usuário pode forçar com "registra isso".

      ## Escrita com reversibilidade (D3 do spec)

      Para cada write em `estado-agente/<arquivo>.md`:

      1. **Backup:** copiar conteúdo atual para `estado-agente/<arquivo>.md.bak` (sobrescreve `.bak` anterior — 1 nível de revert)
      2. **Write:** aplicar a mudança
      3. **Valida:** ler o arquivo de volta e confirmar que tem as seções obrigatórias do template (cabeçalho, blocos "ativos" e "Lições aprendidas")
      4. **Falha:** se a validação falhar, restaurar silentemente do `.bak` e logar internamente. Não emitir mensagem no chat (a falha é um bug do agente, não do usuário).

      ### Comandos de revert do usuário

      | Comando | Efeito |
      |---|---|
      | `reverta` | Restaura os 4 arquivos do `.bak` mais recente |
      | `reverta <arquivo>` | Restaura só o arquivo nomeado (ex: `reverta perfil`) |

      Após qualquer revert, emitir confirmação **curta** no chat: `Restaurei: <lista de arquivos>`. Este é o único caso de quebra de silêncio mid-session.

      Se não houver `.bak` mais recente que o início da sessão atual, declarar: "Sem backup desta sessão para reverter."

      ## Compressão automática durante write

      Antes de persistir cada write, aplicar as regras de `frameworks/compressao-estado.md` ao arquivo afetado. A compressão é parte da escrita, não uma etapa separada.

      ## Resumo de encerramento (D5 do spec)

      Detectar fim de sessão por qualquer um dos sinais:

      - Despedida do usuário: "obrigado", "até depois", "fim", "encerrar", "vou sair", "valeu"
      - Comando explícito: "/save", "encerrar sessão"
      - Declaração de inatividade

      Ao detectar, emitir UM bloco com este formato:

      ```
      ## Estado atualizado nesta sessão

      - **<arquivo>.md:** <descrição curta da mudança>
      - ...

      _Backup salvo. Digite `reverta` para desfazer todas as mudanças, ou `reverta <arquivo>` para desfazer só uma._
      ```

      Se a sessão não produziu nenhum update, emitir apenas:

      ```
      Nenhuma mudança de estado nesta sessão.
      ```

      Não listar não-eventos. Não fazer "resumo das análises da sessão" — o escopo é exclusivamente estado persistido.

      ## Modo manual (sem Write/Edit)

      Se o agente está em modo manual (Projects, ChatGPT, Copilot):

      - Acumular as mudanças internamente durante a sessão
      - **Não anunciar** a captura de cada mudança
      - No fim da sessão (mesmos sinais de despedida), propor o conteúdo dos arquivos atualizados em blocos copiáveis, formato:

      ```
      ## Reescrita proposta — modo manual

      === estado-agente/perfil.md ===
      <conteúdo completo proposto>

      === estado-agente/teses-ativas.md ===
      <conteúdo completo proposto>
      ```

      O usuário copia/aplica nos seus arquivos. Sem revert automático neste modo — backup é responsabilidade do usuário.
      ```

- [ ] Run: `Grep "Detecção de modo \(D6 do spec\)|Eventos que disparam update \(D2 do spec\)|Não-eventos|Escrita com reversibilidade \(D3 do spec\)|Resumo de encerramento \(D5 do spec\)|Modo manual" frameworks/ralph-loop.md` — expect 6 matches
- [ ] Walkthrough: confirmar que cada parte (A-E) de tests/10 tem regra correspondente no framework
- [ ] Commit: `git add frameworks/ralph-loop.md && git commit -m "feat(ralph): add autonomous ralph-loop framework with silent write and revert"`

---

## Task 4: Framework `frameworks/compressao-estado.md`

Files: `frameworks/compressao-estado.md`

Steps:
- [ ] Write `frameworks/compressao-estado.md` com este conteúdo exato:

      ```markdown
      # Framework — Compressão determinística do estado

      Define quando e como itens migram de "ativo" para `## Lições aprendidas (arquivado)` nos arquivos de `estado-agente/`. Aplicado dentro do write, conforme `frameworks/ralph-loop.md`.

      Os três gatilhos abaixo são avaliados em ordem de prioridade. O limite invariante D4.4 prevalece sobre os três.

      ## D4.1 Invalidação imediata (prioridade máxima)

      Aplica quando a sessão atual produz evidência que invalida uma tese ou contradiz uma decisão. Concretamente:

      - Tese referenciada por análise que conclui "premissa falsa", "argumento contrário decisivo", ou "fator macro mudou"
      - Decisão substituída por nova decisão do usuário ("rejeitei o que tinha decidido", "mudei de ideia sobre X")
      - Mudança de perfil torna teses incompatíveis (ex: risco arrojado → conservador → teses de alta volatilidade)

      ### Formato condensado da linha em "Lições aprendidas"

      ```
      - [<ID>, <data criação>→<data invalidação>] <título curto> — <motivo da invalidação>; lição: <generalização útil>.
      ```

      Exemplo:

      ```
      - [T1, 2026-03-15→2026-05-15] Small caps BR concentradas — invalidada quando ciclo de juros foi revertido; lição: concentração setorial sem hedge é frágil a regime macro.
      ```

      ## D4.2 TTL (rede de segurança)

      Aplica a itens que satisfazem **ambas** as condições:

      1. Criado há mais de **90 dias** (data de criação > 90 dias atrás)
      2. Não referenciado em **nenhuma análise dos últimos 30 dias**

      A varredura roda **uma vez por sessão**, no encerramento (não durante turns).

      Itens candidatos são movidos para "Lições aprendidas" e **agregados** em uma linha por categoria/mês:

      ```
      - [<IDs>, <mês/ano>] <categoria> — <resumo agregado>; lição: <generalização>.
      ```

      Exemplo:

      ```
      - [T5, T8, T12 — janeiro 2026] FIIs logísticos — todas as 3 teses se mantiveram conforme esperado; lição agregada: setor previsível em ciclo estável.
      ```

      ## D4.3 Pressão de tamanho (rede de segurança 2)

      Se um arquivo de estado ultrapassar **1.5 KB** após um write, ativar varredura:

      1. Ordenar itens do bloco "ativos" por data de última referência (mais antigo primeiro)
      2. Mover o item mais antigo para "Lições aprendidas"
      3. Repetir até o arquivo ficar **< 1.2 KB**

      Cada item movido recebe linha individual no formato D4.1 (sem motivo de invalidação — substituir por "comprimido por pressão"):

      ```
      - [<ID>, <data criação>→<data compressão>] <título curto> — comprimido por pressão; lição: <inferida do uso>.
      ```

      Se a inferência da lição não for confiável, usar: `; (sem lição clara — referência futura disponível)`.

      ## D4.4 Limite invariante 2 KB (prevalece sobre todos)

      Nenhum arquivo deve exceder **2 KB (2048 bytes)** após qualquer write. Se a varredura D4.3 não conseguir reduzir o arquivo abaixo de 2 KB (por exemplo, todos os itens são recentes e relevantes):

      1. **Não persistir** a nova mudança proposta
      2. Restaurar do `.bak`
      3. Emitir mensagem no chat (única quebra de silêncio para este caso):

      ```
      Arquivo <nome> atingiu o limite de 2 KB — preciso de instrução sua para reorganizar antes de adicionar <novo item>.
      ```

      O usuário pode então:
      - Pedir consolidação manual ("compacte teses-ativas")
      - Explicitar quais teses arquivar
      - Aceitar perda da nova entrada

      ## Ordem de avaliação dentro de um write

      Para cada write proposto:

      1. Aplicar D4.1 (mover invalidações para Lições aprendidas)
      2. Aplicar a mudança principal (novo item ou edit de existente)
      3. Verificar tamanho. Se > 1.5 KB → aplicar D4.3
      4. Verificar tamanho. Se ainda > 2 KB → abortar conforme D4.4 e restaurar .bak

      D4.2 (TTL) é a única regra que **não** roda dentro de cada write — roda só no encerramento da sessão. Ordem no encerramento:

      1. Para cada arquivo, varrer D4.2 e mover candidatos agregados
      2. Verificar D4.4 final (deve estar < 2 KB)
      3. Emitir resumo D5 (`frameworks/ralph-loop.md`)
      ```

- [ ] Run: `Grep "D4.1 Invalidação imediata|D4.2 TTL|D4.3 Pressão de tamanho|D4.4 Limite invariante 2 KB|Ordem de avaliação dentro de um write" frameworks/compressao-estado.md` — expect 5 matches
- [ ] Walkthrough: confirmar que cada Parte (A-D) de tests/11 tem regra correspondente
- [ ] Commit: `git add frameworks/compressao-estado.md && git commit -m "feat(ralph): add deterministic state compression framework"`

---

## Task 5: Atualizar templates `estado-agente/*.template.md` com bloco de Lições aprendidas

Files: `estado-agente/perfil.template.md`, `estado-agente/situacao-relativa.template.md`, `estado-agente/teses-ativas.template.md`, `estado-agente/decisoes.template.md`

Steps:
- [ ] Edit `estado-agente/perfil.template.md`: ao final do arquivo, inserir antes da linha `---` e da "Última revisão":

      ```markdown
      ## Lições aprendidas (arquivado)

      <Itens movidos do bloco ativo conforme `frameworks/compressao-estado.md`. Vazio na primeira sessão.>

      ```

- [ ] Edit `estado-agente/situacao-relativa.template.md`: adicionar a mesma seção `## Lições aprendidas (arquivado)` em local equivalente (antes do divisor final)
- [ ] Edit `estado-agente/teses-ativas.template.md`: idem
- [ ] Edit `estado-agente/decisoes.template.md`: idem
- [ ] Run: `Grep -c "Lições aprendidas \(arquivado\)" estado-agente/` — expect 4 matches (um por template)
- [ ] Commit: `git add estado-agente/ && git commit -m "feat(state): add 'Lições aprendidas' block to all state templates"`

---

## Task 6: Editar `AGENT.md` — referenciar novos frameworks, remover seção antiga

Files: `AGENT.md`

Steps:
- [ ] Read `AGENT.md` linhas 71-90 para localizar a seção `## Protocolo de fim de sessão (escrita de estado — ralph-loop)` atual
- [ ] Edit `AGENT.md`: substituir o bloco completo entre `## Protocolo de fim de sessão (escrita de estado — ralph-loop)` e `## Avisos obrigatórios` (exclusive) por:

      ```markdown
      ## Protocolo de persistência de estado (ralph-loop)

      A persistência de estado é definida em `frameworks/ralph-loop.md` (eventos, escrita, revert, resumo) e `frameworks/compressao-estado.md` (regras de migração ativo → lições aprendidas).

      Comportamento essencial:

      1. **Modo autônomo** (Code/Cowork com Write/Edit): o agente escreve `estado-agente/*.md` silenciosamente durante a sessão; cria `.bak` antes de cada write; aplica compressão automática; emite resumo único no encerramento.
      2. **Modo manual** (Projects, ChatGPT, Copilot): o agente acumula mudanças internamente; no encerramento propõe o conteúdo dos arquivos em blocos copiáveis (modelo pré-v0.3).
      3. **Revert do usuário:** `reverta` ou `reverta <arquivo>` restaura do `.bak` mais recente (modo autônomo apenas).
      4. **Limite invariante:** nenhum arquivo de estado pode exceder 2 KB. Se a compressão falhar, o agente pede instrução.

      Consulte os dois frameworks acima para a especificação completa.

      ```

- [ ] Run: `Grep "frameworks/ralph-loop.md|frameworks/compressao-estado.md" AGENT.md` — expect ≥2 matches
- [ ] Run: `Grep "Proponha ao usuário uma \*\*reescrita\*\*" AGENT.md` — expect 0 matches (o texto antigo foi removido)
- [ ] Run: `Grep "Protocolo de persistência de estado" AGENT.md` — expect 1 match
- [ ] Walkthrough: ler AGENT.md inteiro top-to-bottom, confirmar fluxo coerente sem referências dangling
- [ ] Commit: `git add AGENT.md && git commit -m "feat(agent): reference ralph-loop and compressao-estado frameworks"`

---

## Task 7: Atualizar `guias-instalacao/claude.md` — modo Cowork autônomo

Files: `guias-instalacao/claude.md`

Steps:
- [ ] Edit `guias-instalacao/claude.md`: adicionar nova seção `## Modo autônomo (Cowork / Code)` imediatamente após `## Capacidades de visualização`, com este conteúdo:

      ```markdown
      ## Modo autônomo (Cowork / Code)

      No Claude Cowork ou Code, o agente tem acesso direto a arquivos (ferramentas Write e Edit). Nessa modalidade, o ralph-loop opera em **modo autônomo silencioso**:

      - Mudanças no estado são escritas em `estado-agente/*.md` durante a sessão, sem mensagens no chat
      - Antes de cada write, o agente cria `estado-agente/<arquivo>.md.bak` (1 nível de revert)
      - No encerramento da sessão, o agente emite um resumo único listando arquivos alterados
      - Compressão para "Lições aprendidas" segue regras determinísticas (`frameworks/compressao-estado.md`)

      ### Comandos do usuário

      | Comando | Efeito |
      |---|---|
      | `reverta` | Restaura todos os 4 arquivos de estado do `.bak` |
      | `reverta <arquivo>` | Restaura só o arquivo nomeado (ex: `reverta perfil`) |
      | `/save` | Força resumo de encerramento sem aguardar despedida |

      Backup é por sessão: o `.bak` é sobrescrito quando uma nova escrita acontece. Se você quer reverter, faça antes de a próxima sessão escrever.

      ### Modo Projects (chat web sem Write/Edit)

      No Claude Projects via chat web, o agente cai em modo manual (modelo pré-v0.3): acumula mudanças internamente e no encerramento propõe o conteúdo dos arquivos para você copiar. Sem `.bak` automático nesse modo.
      ```

- [ ] Run: `Grep "Modo autônomo \(Cowork / Code\)|reverta <arquivo>" guias-instalacao/claude.md` — expect 2 matches
- [ ] Commit: `git add guias-instalacao/claude.md && git commit -m "docs(install): document autonomous mode for Claude Cowork/Code"`

---

## Task 8: Atualizar `guias-instalacao/chatgpt.md` e `copilot.md` — declarar modo manual

Files: `guias-instalacao/chatgpt.md`, `guias-instalacao/copilot.md`

Steps:
- [ ] Edit `guias-instalacao/chatgpt.md`: adicionar nova seção `## Modo de persistência de estado` imediatamente antes de `## Limitações nesta plataforma`:

      ```markdown
      ## Modo de persistência de estado

      O ChatGPT Custom GPT **não** tem acesso direto a arquivos da knowledge base. O agente opera em **modo manual** (modelo pré-v0.3):

      - Mudanças de estado são acumuladas internamente durante a sessão (sem ruído no chat)
      - No encerramento, o agente propõe blocos de texto com o conteúdo proposto de cada arquivo
      - Você copia e aplica manualmente nos arquivos locais e reupa na knowledge base do Custom GPT

      O modo autônomo (escrita direta, `.bak`, revert) é exclusivo de Claude Cowork/Code (ver `guias-instalacao/claude.md`).
      ```

- [ ] Edit `guias-instalacao/copilot.md`: adicionar a mesma seção (substituir "ChatGPT Custom GPT" por "Copilot")
- [ ] Run: `Grep -c "Modo de persistência de estado" guias-instalacao/chatgpt.md guias-instalacao/copilot.md` — expect 1 match em cada
- [ ] Commit: `git add guias-instalacao/chatgpt.md guias-instalacao/copilot.md && git commit -m "docs(install): declare manual state mode for ChatGPT and Copilot"`

---

## Task 9: Atualizar `tests/README.md`

Files: `tests/README.md`

Steps:
- [ ] Edit `tests/README.md`: na lista de testes, adicionar após `09-visualizacao-degradacao.md`:

      ```
      - `10-ralph-autonomo.md` — escrita silenciosa, .bak, revert, resumo final, modo Projects sem regressão
      - `11-compressao-determinista.md` — invalidação D4.1, TTL D4.2, pressão D4.3, limite D4.4
      ```

- [ ] Edit `tests/README.md`: substituir "A v0.2.0 só pode ser tagueada quando os 9 testes passarem." por "A v0.3.0 só pode ser tagueada quando os 11 testes passarem."
- [ ] Run: `Grep "10-ralph-autonomo|11-compressao-determinista|v0.3.0" tests/README.md` — expect 3 matches
- [ ] Commit: `git add tests/README.md && git commit -m "docs(tests): register tests 10/11 and bump release criterion to 0.3.0"`

---

## Task 10: Bump `VERSAO.md` e `CHANGELOG.md`

Files: `VERSAO.md`, `CHANGELOG.md`, `AGENT.md`

Steps:
- [ ] Edit `VERSAO.md`: trocar `0.2.0` por `0.3.0` na linha "Versão da skill"
- [ ] Edit `CHANGELOG.md`: abaixo da linha `## [Unreleased]` e antes de `## [0.2.0]`, inserir:

      ```markdown
      ## [0.3.0] — 2026-05-15

      ### Added
      - Framework `frameworks/ralph-loop.md` — ralph-loop autônomo silencioso para Claude Code/Cowork
      - Framework `frameworks/compressao-estado.md` — regras determinísticas D4.1-D4.4 (invalidação, TTL, pressão, limite 2 KB)
      - Bloco `## Lições aprendidas (arquivado)` em todos os templates de `estado-agente/`
      - Comandos de revert: `reverta` e `reverta <arquivo>` (modo autônomo)
      - Test cases 10 (ralph-loop autônomo) e 11 (compressão determinística)

      ### Changed
      - `AGENT.md`: protocolo de persistência referencia os dois novos frameworks; seção antiga de fim de sessão substituída
      - `guias-instalacao/claude.md`: documenta modo autônomo Cowork/Code com `.bak` e revert
      - `guias-instalacao/chatgpt.md` + `copilot.md`: explicitam modo manual

      ### Behavior change (modo autônomo)
      - No Claude Code/Cowork, mudanças de estado são escritas silenciosamente durante a sessão
      - Resumo único de mudanças no encerramento da sessão
      - Backup automático (`.bak`) por arquivo, sobrescrito a cada nova escrita
      ```

- [ ] Edit `AGENT.md`: atualizar a linha `**Versão da skill:** v0.2 — corte do conhecimento: 2026-05` no bloco "Cabeçalho de cada output" → `**Versão da skill:** v0.3 — corte do conhecimento: 2026-05`
- [ ] Run: `Grep "0\.3\.0" VERSAO.md CHANGELOG.md` — expect ≥2 matches; `Grep "v0\.3" AGENT.md` — expect 1 match
- [ ] Commit: `git add VERSAO.md CHANGELOG.md AGENT.md && git commit -m "chore: release 0.3.0 — autonomous ralph-loop and deterministic compression"`

---

## Task 11: Merge, push e tag

Files: nenhum (operação git)

Steps:
- [ ] Run: `git log --oneline main..HEAD` no worktree — confirmar que os 10 commits anteriores estão limpos e em ordem
- [ ] Run no checkout main: `git merge feat/ralph-loop-autonomo --ff-only`
- [ ] Run: `git push origin main`
- [ ] Run: `git tag -a v0.3.0 -m "v0.3.0 — autonomous ralph-loop"` e `git push origin v0.3.0`
- [ ] Run: `gh release create v0.3.0 --title "v0.3.0 — Autonomous ralph-loop" --notes "..."` (notas a partir do CHANGELOG)
- [ ] Cleanup: `git worktree remove ../finance-advisor-ralph` e `git branch -d feat/ralph-loop-autonomo`

---

## Self-review checklist

- [x] D1 (plataforma e modo) — Tasks 3 (framework), 7 (guia Claude), 8 (guias ChatGPT/Copilot)
- [x] D2 (cadência contínua silenciosa) — Task 3 (eventos + não-eventos)
- [x] D3 (escrita com reversibilidade) — Task 3 (escrita, .bak, revert)
- [x] D4 (compressão D4.1-D4.4) — Task 4
- [x] D5 (resumo de encerramento) — Task 3 (formato e sinais de fim)
- [x] D6 (detecção de modo) — Task 3 (Detecção de modo no topo)
- [x] Todos os 15 arquivos do spec aparecem em alguma task
- [x] Tests 10 e 11 cobrem todos os 8 critérios de aceitação do spec
- [x] Nenhum placeholder ("TBD", "implementar adequadamente")
- [x] Cada task tem grep determinístico para confirmar green
- [x] Tarefas 2-5 min cada (Tasks 3 e 4 são as maiores — texto volumoso, ainda < 10 min)
- [x] Ordem: tests primeiro (red), framework depois (green), templates e guides, depois bump

## Riscos de execução

- **Detecção de modo (D6) é heurística**: o framework diz "verifica se Write/Edit estão disponíveis", mas o agente não tem como inspetar tooling diretamente em todos os ambientes. Mitigação: o framework declara o critério como conceitual; na prática Claude Code/Cowork sempre tem essas tools, e Projects nunca tem.
- **Compressão D4.2 depende de "data de criação" e "data de última referência"**: os templates atuais não têm campos explícitos para isso. A maioria das entradas tem data implícita no formato `[T1, 2026-03-15]`. Task 5 não adiciona campo de "última referência" — confiar em heurística textual. Aceitável para v0.3.0; refinar em v0.4 se necessário.
- **Test cases não são executáveis automaticamente**: dependem de walkthrough manual contra Claude Code real para confirmar comportamento. Plano garante que o framework REGRA está presente; o teste empírico do agente é responsabilidade do release.
