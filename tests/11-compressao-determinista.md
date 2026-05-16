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
