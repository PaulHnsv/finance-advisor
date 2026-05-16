# Instalação no Microsoft Copilot ou outros assistentes

Esta plataforma tem mais limitações que Claude Projects e ChatGPT Custom GPT. O agente funciona em modo "degradado" — ainda é viável, mas o usuário precisa fazer mais trabalho manual a cada sessão.

## Antes de começar

Leia o [guia de segurança do usuário](seguranca-do-usuario.md).

## Estratégia geral

Como o Copilot e similares geralmente não têm um conceito robusto de "Projeto com knowledge base persistente", a estratégia é:

1. Manter o conteúdo da skill em arquivos locais
2. A cada sessão, montar um prompt inicial que carregue (a) o AGENT.md, (b) os frameworks relevantes para o tipo de análise, (c) o estado atual
3. Ao fim, copiar o estado proposto pelo agente de volta para os arquivos locais

## Passo 1: prepare o "kit local"

Em uma pasta local, mantenha:

- `AGENT.md`
- `VERSAO.md`
- `frameworks/` completos
- `estado-agente/` com os arquivos preenchidos (perfil, situação relativa, teses ativas, decisões)

## Passo 2: estrutura do prompt inicial de sessão

Cada vez que você abrir uma conversa nova com o assistente, comece com algo equivalente a:

```
Vou colar abaixo um agente persistente especializado em análise financeira. Após ler tudo, você deve assumir essa identidade. Antes de responder, leia também o estado atual do usuário e os frameworks relevantes que vou colar em seguida.

=== AGENT.md ===
[colar conteúdo do AGENT.md]

=== VERSAO.md ===
[colar conteúdo do VERSAO.md]

=== Estado do usuário ===
[colar conteúdo de estado-agente/perfil.md]
[colar conteúdo de estado-agente/situacao-relativa.md]
[colar conteúdo de estado-agente/teses-ativas.md]
[colar conteúdo de estado-agente/decisoes.md]

=== Frameworks aplicáveis a esta sessão ===
[colar os frameworks mais provavelmente úteis — sempre output-review-ready.md e segunda-opiniao.md, mais o(s) framework(s) temático(s)]

A partir daqui, atue como o agente. Confirme que entendeu e aguarde minha pergunta.
```

## Passo 3: faça a pergunta

Após o assistente confirmar, faça sua pergunta normalmente.

## Passo 4: ralph-loop manual ao final

Peça: "proponha a reescrita dos arquivos de estado mantendo cada um abaixo de 2 KB."

Copie cada bloco proposto e cole no arquivo local correspondente, substituindo o conteúdo antigo. Salve.

## Otimizações

- Mantenha um "prompt template" salvo em um app de notas para colar rapidamente
- Limite os frameworks anexados aos estritamente necessários — Copilot tem janela de contexto menor que Claude/ChatGPT
- Para sessões curtas (pergunta única), pode ser viável anexar apenas AGENT.md + estado + 1 framework temático

## Considerações de privacidade

- Microsoft Copilot empresarial pode ter políticas diferentes do Copilot grátis — verifique
- Não cole o conteúdo do AGENT.md em assistentes desconhecidos que possam logar e revender prompts

## Capacidades de visualização

O Copilot não tem Artifacts nem Code Interpreter para renderização. Resultado:

| Gráfico | Suporte no Copilot |
|---|---|
| V1 (radar) | **Indisponível** — agente declara: "Visualização V1 indisponível nesta plataforma; ver tabela acima." |
| V2 (pizza) | **Mermaid `pie`** — funciona em renderizadores compatíveis (GitHub, alguns clients) |
| V3 (quadrante) | **Mermaid `quadrantChart`** — idem |

Em ambientes onde o cliente do usuário não renderiza Mermaid, o bloco aparece como texto bruto. Isso é aceito: o conteúdo da análise (tabelas + texto) já é completo.

## Modo de persistência de estado

O Copilot **não** tem acesso direto a arquivos da knowledge base. O agente opera em **modo manual** (modelo pré-v0.3):

- Mudanças de estado são acumuladas internamente durante a sessão (sem ruído no chat)
- No encerramento, o agente propõe blocos de texto com o conteúdo proposto de cada arquivo
- Você copia e aplica manualmente nos arquivos locais e reupa na knowledge base do Copilot

O modo autônomo (escrita direta, `.bak`, revert) é exclusivo de Claude Cowork/Code (ver `guias-instalacao/claude.md`).

## Limitações nesta plataforma

- Sessão não persiste entre conversas — você precisa colar AGENT.md a cada vez
- Janela de contexto menor pode forçar você a omitir alguns frameworks
- Sem indicação visual clara de "estado carregado" — fica a critério do agente confirmar
- Ralph-loop totalmente manual e mais propenso a erro humano

Para uso intensivo, considere migrar para Claude Projects ou ChatGPT Custom GPT.
