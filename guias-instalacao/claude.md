# Instalação no Claude (Projects / Cowork)

Esta é a plataforma recomendada para o agente, pois o Claude consegue ler e escrever os arquivos de estado automaticamente dentro de um Project.

## Antes de começar

Leia o [guia de segurança do usuário](seguranca-do-usuario.md). Não envie dados sensíveis nem durante o setup nem durante o uso.

## Passos

### 1. Crie um novo Project

Acesse o Claude (https://claude.ai) e crie um novo Project chamado, por exemplo, "Finance Advisor".

### 2. Configure as "Custom Instructions" do Project

Cole o conteúdo completo de `AGENT.md` no campo de instruções customizadas do Project. Este é o "system prompt" que define a identidade e os protocolos do agente.

### 3. Faça upload dos arquivos do projeto

Adicione ao Project os seguintes arquivos:

- `VERSAO.md`
- Todos os arquivos da pasta `frameworks/` (incluindo subpasta `tributacao/`)
- Os templates da pasta `estado-agente/` — renomeie cada um removendo o sufixo `.template` (ex: `perfil.template.md` vira `perfil.md`). Esses serão os arquivos que o agente vai preencher ao longo do uso.

### 4. Inicie a primeira sessão

Mande a primeira mensagem ao Claude, por exemplo:

> Inicie o intake do perfil. É a minha primeira conversa.

O agente vai:

1. Ler o VERSAO.md
2. Detectar que o perfil está vazio
3. Iniciar o intake conforme `frameworks/perfil-investidor.md`
4. Fazer uma pergunta por turno

### 5. Sessões seguintes

Em todas as sessões posteriores, o Claude lê automaticamente os arquivos do Project. Você pode começar diretamente com a sua dúvida ou análise pretendida.

### 6. Ralph-loop (atualização do estado)

O comportamento depende do modo (veja seções abaixo):

- Em **Claude Cowork ou Code** (modo autônomo): o agente escreve o estado silenciosamente durante a sessão; ao final emite um resumo único. Sem ação necessária da sua parte.
- Em **Claude Projects via chat web** (modo manual): o agente vai **propor** uma reescrita dos arquivos em `estado-agente/` ao fim da sessão. Revise e aprove. Você pode pedir explicitamente: "atualize os arquivos de estado agora".

Detalhes nas seções "Modo autônomo (Cowork / Code)" e "Modo Projects" mais abaixo.

## Considerações de privacidade

- Verifique nas configurações da sua conta Claude se há opção de não usar conversas para treinamento — habilite se disponível
- O Project é privado à sua conta. Não compartilhe o link do Project com terceiros se ele contiver detalhes sensíveis do seu estado
- Antes de excluir o Project, exporte os arquivos de estado se quiser preservar a memória

## Capacidades de visualização

O Claude Projects oferece **Artifacts**, que renderizam React + Recharts inline. Os três gráficos do catálogo de `frameworks/visualizacao.md` (V1 radar, V2 pizza, V3 quadrante) funcionam todos. Use Artifacts independentes ou um único Artifact agrupando múltiplos gráficos da mesma análise.

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

## Limitações nesta plataforma

- O Claude pode hesitar em executar escrita automática em alguns ambientes. Se ele apenas mostrar o conteúdo proposto, copie e salve manualmente
- O tamanho total dos arquivos do Project tem limite — o ralph-loop é essencial para manter os arquivos pequenos
- Artifacts têm limite de tamanho (kilobytes); para análises com muitos gráficos, dividir em Artifacts separados
