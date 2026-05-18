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

## Atualização (já está instalado, quero subir de versão)

Como o Copilot usa um "kit local" de arquivos colados a cada sessão, a atualização é só atualizar os arquivos locais. Sua próxima sessão automaticamente usa a versão nova quando você colar `AGENT.md` no prompt inicial.

### Antes de atualizar

1. **Cheque a versão atual** — no início de uma sessão antiga (com kit antigo), pergunte "qual sua versão da skill" (ele lê seu `VERSAO.md` colado)
2. **Cheque a versão nova** em [github.com/PaulHnsv/finance-advisor/releases](https://github.com/PaulHnsv/finance-advisor/releases)
3. **Leia o CHANGELOG** entre as duas versões — atenção a "Behavior change"
4. **Faça backup dos seus `estado-agente/*.md` preenchidos** — copie para uma pasta paralela antes de qualquer mexida

### Passos

**Opção A — você clonou o repo:**

```bash
cd <pasta do kit>
git stash               # guarda mudanças locais não-commitadas (se houver)
git pull origin main
git stash pop           # restaura suas mudanças locais
```

Se você manteve os arquivos preenchidos em `estado-agente/` sem commit (o que é o esperado — `.gitignore` ignora eles), o `git pull` não toca neles.

**Opção B — você baixou ZIP:**

1. Baixe o ZIP da versão nova do repo
2. Extraia em uma pasta nova
3. **Copie seus `estado-agente/*.md` preenchidos** da pasta antiga para a nova
4. Use a pasta nova como kit a partir da próxima sessão

### Migração de templates (v0.1.x ou v0.2.x → v0.3.x)

A v0.3.0 adicionou o bloco `## Lições aprendidas (arquivado)` em todos os templates. Para seus arquivos preenchidos antigos:

- Edite cada `estado-agente/*.md` adicionando o bloco antes do divisor `---` final:
  ```markdown
  ## Lições aprendidas (arquivado)

  <vazio até o agente arquivar algo>

  ---
  Última revisão: ...
  ```

Sem essa migração, as regras de compressão da v0.3 (`frameworks/compressao-estado.md`) ainda funcionam, mas o agente vai precisar criar o bloco na primeira sessão pós-update — você verá isso no resumo final.

### Verificar pós-update

Na próxima sessão (com kit novo colado no prompt inicial), pergunte: "qual sua versão da skill?" — deve retornar a versão nova.

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
