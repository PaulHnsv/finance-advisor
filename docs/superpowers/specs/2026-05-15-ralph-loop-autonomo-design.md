# Spec — Ralph-loop autônomo (escrita silenciosa de estado)

**Data:** 2026-05-15
**Status:** Design aprovado, pendente plano de implementação
**Autor:** brainstorming session com o mantenedor
**Versão alvo:** 0.3.0

## Motivação

O ralph-loop atual exige três ações manuais do usuário a cada sessão: lembrar de pedir a atualização, ler o conteúdo proposto, e aplicar a mudança nos arquivos (copy-paste no caso de Claude Projects e ChatGPT). Na prática isso significa que o estado fica desatualizado — o usuário esquece, ou pula a etapa por preguiça, e a sessão seguinte parte de um estado obsoleto.

Em Claude Code / Cowork existe acesso a arquivos. Podemos fazer o agente escrever sozinho, silenciosamente, durante a sessão. O usuário só vê o resultado: um resumo no fim. Isso transforma persistência de uma tarefa do usuário em uma propriedade automática do sistema.

## Escopo

Este spec cobre **a automação do ralph-loop em plataformas com acesso a arquivos** (primário: Claude Code / Cowork). Para plataformas sem acesso (Claude Projects, ChatGPT Custom GPT, Copilot) o fluxo manual atual é mantido sem regressão — apenas documentado como modo degradado.

Está fora de escopo:
- Sincronização entre plataformas (perfil escrito em Code ≠ perfil no Projects)
- Versionamento via git automático do `estado-agente/`
- Helper CLI para aplicar patches em plataformas não-Cowork

## Decisões de design

### D1. Plataforma e modo

| Plataforma | Modo |
|---|---|
| Claude Code / Cowork | **Autônomo silencioso** (este spec) |
| Claude Projects | **Manual atual** — agente propõe, usuário aplica |
| ChatGPT Custom GPT | **Manual atual** — agente propõe, usuário aplica + reupload |
| Copilot | **Manual atual** — idem |

A detecção de plataforma é por capacidade: se o agente tem ferramentas Write/Edit disponíveis, opera em modo autônomo. Senão, manual.

### D2. Cadência: contínua e silenciosa

O agente atualiza arquivos de estado conforme eventos detectados durante a conversa, sem emitir nenhuma mensagem no chat por update. O usuário não vê "salvei perfil.md" a cada turno.

**Eventos de update por arquivo:**

| Arquivo | Evento de update |
|---|---|
| `perfil.md` | Usuário declara nova informação de perfil: faixa etária, jurisdição, dependentes, estabilidade de renda, tolerância a risco, horizonte, objetivos, restrições |
| `situacao-relativa.md` | Usuário declara nova informação financeira em faixas ou percentuais (ex: "minha reserva cobre 4 meses", "tenho 60% em renda fixa") |
| `teses-ativas.md` | Análise produz nova tese; tese existente é referenciada com nova evidência (refina ou desafia); usuário relata desfecho de uma tese |
| `decisoes.md` | Usuário declara que aplicou recomendação ("vou seguir essa", "comprei tal coisa", "rejeitei pelo motivo X") |

**Não-eventos** (não disparam update):
- Pergunta exploratória sem declaração de fato persistente
- Análise produzida pelo agente sem evidência de adoção pelo usuário
- Especulação ou "what if" do usuário sem declaração de mudança real

### D3. Escrita com reversibilidade

Antes de cada write em `estado-agente/<arquivo>.md`:

1. Copiar conteúdo atual para `estado-agente/<arquivo>.md.bak` (sobrescreve o `.bak` anterior — apenas 1 nível de revert)
2. Aplicar o write
3. Se o write produzir markdown inválido (front-matter quebrado, seção mandatória ausente), restaurar do `.bak` silentemente e logar internamente

**Comando de revert do usuário:**

- `reverta` (sem argumento) — restaura todos os 4 arquivos do `.bak` mais recente
- `reverta <arquivo>` — restaura só o arquivo nomeado
- Se não houver `.bak` mais recente que a sessão, agente declara: "Sem backup desta sessão para reverter"

Após qualquer revert, o agente emite confirmação imediata no chat (única quebra do silêncio mid-session).

### D4. Compressão determinística

Itens podem mover de "ativo" para o bloco `## Lições aprendidas (arquivado)` do mesmo arquivo por **três gatilhos**, em ordem de prioridade:

#### D4.1 Invalidação imediata (prioridade máxima)

Sempre que uma sessão produzir evidência que invalida uma tese ou contradiz uma decisão registrada, o agente move o item imediatamente. Critérios concretos:

- Tese referenciada por análise que conclui "premissa falsa" ou "argumento contrário decisivo"
- Decisão substituída por nova decisão do usuário ("rejeitei o que eu tinha decidido")
- Mudança de perfil torna uma tese inadequada (ex: usuário muda risco de arrojado para conservador → teses de alta volatilidade são arquivadas)

Item movido vira uma linha condensada no bloco "Lições aprendidas" com formato:

```markdown
- [T1, 2026-03-15→2026-05-15] Small caps BR alta convicção — invalidada quando setor X colapsou; lição: concentração setorial sem hedge.
```

#### D4.2 TTL (rede de segurança)

Item criado há mais de 90 dias e não referenciado em nenhuma análise dos últimos 30 dias é candidato. Executado uma vez no fim da sessão (não a cada turno). Itens candidatos são movidos para "Lições aprendidas" agregados em uma linha por categoria:

```markdown
- [T5, T8, T12 — janeiro 2026] FIIs logísticos — todas as 3 teses se mantiveram conforme esperado; lição agregada: setor previsível em ciclo estável.
```

#### D4.3 Pressão de tamanho (rede de segurança 2)

Se um arquivo de estado ultrapassar 1.5 KB após o write da sessão, agente varre os itens mais antigos do bloco "ativo" e move para "Lições aprendidas" até o arquivo ficar < 1.2 KB. Itens são selecionados pelo critério: data de última referência mais antiga primeiro.

#### D4.4 Limite invariante

Mesmo com os três gatilhos, **nenhum arquivo deve passar de 2 KB** após qualquer write. Se a varredura D4.3 não consegue trazer abaixo de 2 KB, agente para de aceitar novos updates naquele arquivo e declara no chat: "Arquivo X atingiu o limite — preciso de instrução sua para reorganizar."

### D5. Resumo de encerramento

O único momento em que o agente quebra o silêncio sobre persistência é ao detectar fim de sessão. Sinais de fim de sessão:

- Despedida do usuário ("obrigado", "até depois", "fim", "encerrar")
- Comando explícito "/save", "encerrar sessão" ou similar
- Inatividade declarada pelo usuário ("vou sair")

Formato do resumo (modelo):

```
## Estado atualizado nesta sessão

- **perfil.md:** tolerância de risco moderada → arrojada (você declarou na primeira pergunta)
- **teses-ativas.md:** +T3 (small caps BR concentradas) | −T1 movida pra lições (invalidada por X)
- **decisoes.md:** +D5 (rebalanceamento adiado por motivo fiscal)

_Backup salvo. Digite `reverta` para desfazer estas 3 mudanças, ou `reverta <arquivo>` para desfazer só uma._
```

Se a sessão não produziu nenhum update de estado, agente diz apenas: "Nenhuma mudança de estado nesta sessão."

### D6. Detecção de modo (Code vs Projects)

No início de cada sessão, durante o protocolo de leitura de estado (já existente em AGENT.md), o agente verifica se tem ferramentas de escrita disponíveis. Concretamente:

- Se houver `Write` ou `Edit` tool exposto na sessão → **modo autônomo**
- Senão → **modo manual** (comportamento atual)

O agente NÃO declara o modo no chat. A declaração só aparece se:
- Em modo autônomo, o usuário pedir "como você está salvando o estado" → agente explica
- Em modo manual, o usuário declarar uma info nova que normalmente disparia update → agente prossegue sem emitir nada (o ralph-loop manual já trata isso no fim da sessão)

## Mudanças no repositório

| Arquivo | Tipo | Conteúdo |
|---|---|---|
| `frameworks/ralph-loop.md` | **novo** | Protocolo dedicado: eventos D2, escrita+revert D3, encerramento D5, detecção D6 |
| `frameworks/compressao-estado.md` | **novo** | Regras D4.1-D4.4 com exemplos |
| `AGENT.md` | edição | Substituir seção "Protocolo de fim de sessão" por referência ao novo framework; adicionar referência ao framework de compressão |
| `estado-agente/perfil.template.md` | edição | Adicionar bloco `## Lições aprendidas (arquivado)` vazio |
| `estado-agente/situacao-relativa.template.md` | edição | Idem |
| `estado-agente/teses-ativas.template.md` | edição | Idem |
| `estado-agente/decisoes.template.md` | edição | Idem |
| `guias-instalacao/claude.md` | edição | Documentar modo Cowork autônomo; explicar `.bak` e revert |
| `guias-instalacao/chatgpt.md` | edição | Declarar que modo autônomo é Cowork-only; mantém manual |
| `guias-instalacao/copilot.md` | edição | Idem |
| `tests/10-ralph-autonomo.md` | **novo** | Testes: silêncio durante sessão; resumo no fim; .bak presente; revert funciona |
| `tests/11-compressao-determinista.md` | **novo** | Testes: invalidação imediata, TTL > 90 dias, pressão > 1.5 KB, limite 2 KB |
| `tests/README.md` | edição | Adicionar 10/11 à lista; mover release para 0.3.0 |
| `VERSAO.md` | edição | Bump 0.2.0 → 0.3.0 |
| `CHANGELOG.md` | edição | Entrada [0.3.0] |

## Critérios de aceitação

1. Sessão em Claude Code: usuário declara mudança de jurisdição BR→PT. Agente escreve `perfil.md` sem emitir mensagem no chat. Após write, `perfil.md.bak` existe contendo o conteúdo anterior. No fim da sessão, resumo lista a mudança.
2. Mesmo cenário em Claude Projects (sem Write tool): agente NÃO escreve durante a sessão. No fim, agente propõe a reescrita como hoje. Sem regressão.
3. Sessão em Claude Code com 3 teses ativas: análise demonstra que T2 está invalidada. Agente move T2 para "Lições aprendidas" no mesmo write, com formato condensado D4.1.
4. Arquivo `teses-ativas.md` chega a 1.6 KB após write: agente varre, move o item mais antigo do bloco ativo para "Lições aprendidas", reduzindo o arquivo para 1.18 KB.
5. Arquivo chega a 2.0 KB e D4.3 não consegue reduzir abaixo (todos os itens são recentes): agente declara no chat e para de aceitar updates naquele arquivo até instrução.
6. Após sessão com 2 mudanças, usuário digita "reverta": agente restaura ambos os arquivos do `.bak`, emite confirmação.
7. Usuário digita "reverta perfil": agente restaura só `perfil.md` do `.bak`.
8. Sessão sem mudanças relevantes: resumo final diz "Nenhuma mudança de estado nesta sessão."

## Riscos e mitigações

| Risco | Mitigação |
|---|---|
| Agente detecta como "evento" algo que era especulação do usuário, polui estado | D2 explicita "não-eventos" (perguntas exploratórias, what-if, especulação). Test 10 cobre falso positivo. |
| Write produz markdown corrompido (parser quebrado) | D3 valida pós-write contra um schema mínimo (presença de seção obrigatória); restaura silentemente do .bak em caso de falha. |
| Revert tarde demais: usuário pediu revert mas .bak já foi sobrescrito por nova sessão | .bak é por arquivo, sobrescrito a cada write. Mitigação: D5 declara "Backup salvo" no fim da sessão, sinalizando a janela. Usuário sabe que precisa reverter logo. |
| Compressão por TTL move itens ainda relevantes | TTL exige 2 condições simultâneas (>90 dias + não referenciado em 30 dias). Itens que aparecem em análise recente são automaticamente protegidos. |
| Agente em modo Projects acidentalmente tenta escrever (porque Cowork-like ferramentas aparecem temporariamente) | D6 verifica a cada sessão (não cacheia o modo). Test 10 inclui simulação de troca. |
| Bloco "Lições aprendidas" cresce indefinidamente | Critério D4.4 (limite 2 KB) inclui o bloco. Quando o arquivo todo passa de 2 KB, agente para. Lições muito antigas podem ser agregadas em uma linha por trimestre. |

## Fora de escopo (para specs futuras)

- Sincronização cross-platform (Code escreve, Projects lê — mas Projects não escreve)
- Versionamento git automático do `estado-agente/` (git commit a cada sessão)
- Múltiplos níveis de .bak (histórico de N versões em vez de só a anterior)
- Helper CLI para aplicar diffs em ChatGPT/Copilot
- Compressão semântica via LLM auxiliar (vs as regras determinísticas deste spec)
- Sincronização entre múltiplos dispositivos do mesmo usuário
