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

Nenhum arquivo deve exceder **2 KB (2048 bytes UTF-8 — caracteres acentuados contam como 2+ bytes; medir com `wc -c`)** após qualquer write. Se a varredura D4.3 não conseguir reduzir o arquivo abaixo de 2 KB (por exemplo, todos os itens são recentes e relevantes):

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
