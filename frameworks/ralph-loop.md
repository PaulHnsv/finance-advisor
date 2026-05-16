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
| `reverta <arquivo>` | Restaura só o arquivo nomeado. Extensão `.md` é inferida automaticamente (ex: `reverta perfil` → `estado-agente/perfil.md`) |

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
