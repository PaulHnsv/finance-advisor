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
