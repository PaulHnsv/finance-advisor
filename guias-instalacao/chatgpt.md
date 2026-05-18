# Instalação no ChatGPT (Custom GPT)

## Antes de começar

Leia o [guia de segurança do usuário](seguranca-do-usuario.md).

Você precisa de uma assinatura paga do ChatGPT (Plus ou superior) para criar Custom GPTs.

## Passos

### 1. Crie um Custom GPT

Em https://chatgpt.com, abra "Explore GPTs" → "Create" para iniciar um novo Custom GPT.

Dê um nome (ex: "Finance Advisor") e uma descrição curta.

### 2. Configure as instructions

No campo "Instructions", cole o conteúdo completo de `AGENT.md`.

### 3. Faça upload da knowledge base

Em "Knowledge", adicione os arquivos:

- `VERSAO.md`
- Todos os arquivos da pasta `frameworks/`, incluindo `frameworks/tributacao/BR.md` e `frameworks/tributacao/US.md`
- Os templates da pasta `estado-agente/` (renomeados sem o `.template`)

### 4. Configure as Capabilities

- Habilite **"Code Interpreter" / "Advanced Data Analysis"** se quiser visualizações inline (ver "Capacidades de visualização" abaixo). Sem ele, o agente cai em modo só-tabela.
- "Web Browsing" pode ser útil para verificar atualizações regulatórias, mas pode introduzir alucinação; deixe a critério
- "DALL-E" não é necessário

### 5. Salve

Salve o GPT como "Only me" (ou "Anyone with a link" se quiser compartilhar com outras pessoas — verifique se isso é compatível com sua intenção).

### 6. Inicie a primeira sessão

Mensagem inicial sugerida:

> Inicie o intake do perfil. É a minha primeira conversa.

## Limitação importante — ralph-loop manual

Diferente do Claude Projects, o ChatGPT **não permite que o Custom GPT escreva diretamente nos arquivos da knowledge base**. Isso significa que o ralph-loop precisa ser executado manualmente:

### Fluxo manual

1. Ao fim de cada sessão, peça ao agente: "proponha a reescrita dos arquivos de estado"
2. O agente vai gerar o conteúdo proposto em texto
3. Você precisa **copiar** esse conteúdo
4. Editar manualmente os arquivos locais em `estado-agente/`
5. Voltar à configuração do Custom GPT e fazer upload das versões atualizadas (remover as antigas primeiro)

É inconveniente, mas necessário pela arquitetura da plataforma.

### Atalho recomendado

- Mantenha os arquivos de `estado-agente/` em uma pasta local sincronizada (Google Drive, Dropbox, OneDrive)
- Use sempre o mesmo Custom GPT e re-upload os arquivos em ciclos espaçados (semanal ou quinzenal)

## Considerações de privacidade

- Habilite "Data controls" → desativar o uso de conversas para treino, se disponível na sua região
- Não compartilhe o GPT publicamente se ele tem estado preenchido com suas informações
- A OpenAI pode reter logs conforme política da plataforma; verifique antes de usar para finanças

## Capacidades de visualização

Os gráficos do catálogo `frameworks/visualizacao.md` dependem do **Code Interpreter** (também chamado de "Advanced Data Analysis"). Habilite-o nas configurações do Custom GPT.

Com Code Interpreter ligado:

- V1 (radar), V2 (pizza), V3 (quadrante) renderizam como PNG inline via matplotlib
- Use a paleta definida no framework

Sem Code Interpreter, o agente deve declarar: "Visualização V_n_ indisponível nesta sessão (Code Interpreter desligado); ver tabela acima." e seguir só com tabelas.

## Atualização (já está instalado, quero subir de versão)

### Antes de atualizar

1. **Cheque a versão atual** — pergunte ao Custom GPT "qual sua versão da skill" (ele lê `VERSAO.md` da knowledge base)
2. **Cheque a versão nova** em [github.com/PaulHnsv/finance-advisor/releases](https://github.com/PaulHnsv/finance-advisor/releases)
3. **Leia o CHANGELOG** entre as duas versões — atenção a "Behavior change"
4. **Faça backup dos seus arquivos preenchidos em `estado-agente/`** (não os templates) — baixe da knowledge base antes de qualquer mexida

### Passos

1. Baixe os arquivos da versão nova do repo
2. No editor do Custom GPT, abra a knowledge base
3. **Remova:** `VERSAO.md`, todos os arquivos de `frameworks/` (incluindo `tributacao/`)
4. **Upload:** os mesmos arquivos da versão nova
5. **Atualize as Instructions** com o novo conteúdo de `AGENT.md`
6. **NÃO mexa** nos arquivos de `estado-agente/` já preenchidos (perfil.md, situacao-relativa.md, teses-ativas.md, decisoes.md) — eles têm seu estado real
7. Salve o Custom GPT

### Migração de templates (v0.1.x ou v0.2.x → v0.3.x)

A v0.3.0 adicionou o bloco `## Lições aprendidas (arquivado)` em todos os templates de estado. Se seus arquivos preenchidos vêm de v0.1 ou v0.2:

- **Recomendado:** baixe cada `estado-agente/*.md` da knowledge, adicione manualmente o bloco antes do divisor `---` final, e re-upe.
- **Alternativa:** abra uma sessão pós-update e peça "atualize o formato dos meus arquivos de estado para v0.3, adicionando o bloco de Lições aprendidas". O GPT produz o conteúdo proposto — você copia para os arquivos locais e re-upa.

### Verificar pós-update

Pergunte: "qual sua versão da skill?" — deve retornar a versão nova.

## Modo de persistência de estado

O ChatGPT Custom GPT **não** tem acesso direto a arquivos da knowledge base. O agente opera em **modo manual** (modelo pré-v0.3):

- Mudanças de estado são acumuladas internamente durante a sessão (sem ruído no chat)
- No encerramento, o agente propõe blocos de texto com o conteúdo proposto de cada arquivo
- Você copia e aplica manualmente nos arquivos locais e reupa na knowledge base do Custom GPT

O modo autônomo (escrita direta, `.bak`, revert) é exclusivo de Claude Cowork/Code (ver `guias-instalacao/claude.md`).

## Limitações nesta plataforma

- Ralph-loop manual (descrito acima)
- Tamanho da knowledge base tem limite — o ralph-loop manual ajuda a manter dentro do limite
- Browsing introduz risco de informação desatualizada ou imprecisa entrando na análise sem sinalização
