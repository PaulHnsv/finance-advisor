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

- Mantenha "Code Interpreter" desabilitado (não é necessário)
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

## Limitações nesta plataforma

- Ralph-loop manual (descrito acima)
- Tamanho da knowledge base tem limite — o ralph-loop manual ajuda a manter dentro do limite
- Browsing introduz risco de informação desatualizada ou imprecisa entrando na análise sem sinalização
