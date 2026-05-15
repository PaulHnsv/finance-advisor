# Arquitetura

Esta documentação é voltada para **contribuintes** do projeto. Para usuários, ver os guias em `guias-instalacao/`.

## Visão geral

O finance-advisor é um agente persistente empacotado como conjunto de arquivos markdown. O agente roda dentro de plataformas de IA conversacional (Claude, ChatGPT, Copilot) usando o arquivo `AGENT.md` como instrução principal, e carrega arquivos auxiliares conforme a demanda da sessão.

## Componentes

```
finance-advisor/
├── AGENT.md                          ← Núcleo: identidade e protocolos
├── VERSAO.md                         ← Data de corte e versão atual
├── frameworks/                       ← Conhecimento por domínio
│   ├── perfil-investidor.md
│   ├── analise-risco.md
│   ├── financas-pessoais.md
│   ├── investimentos.md
│   ├── valuation-acoes.md
│   ├── financas-empresariais.md
│   ├── segunda-opiniao.md
│   ├── output-review-ready.md
│   └── tributacao/                   ← Isolada por jurisdição
├── estado-agente/                    ← Memória persistente do usuário
├── guias-instalacao/                 ← Setup por plataforma
├── tests/                            ← Casos de teste manuais
└── docs/                             ← Documentação para contribuintes
```

## Princípio: responsabilidade única por arquivo

- `AGENT.md` define **comportamento**, não conhecimento de domínio
- Cada `frameworks/*.md` é um tema autocontido com definições, perguntas-chave, exemplos e armadilhas
- `estado-agente/*.md` é só dado, sem instruções
- Cada `tests/*.md` valida **um** comportamento ou formato
- Os guias por plataforma não se sobrepõem — cada um cobre uma plataforma

Esse princípio mantém o sistema legível para humanos e fácil de evoluir.

## Fluxo de uma sessão

```
[Início]
  │
  ├── Lê VERSAO.md (data de corte)
  ├── Lê estado-agente/* (perfil, situação, teses, decisões)
  │
  ├── [Se perfil vazio → dispara intake]
  │
[Recepção do pedido]
  │
  ├── Verificação anti-dados-sensíveis
  ├── Classificação (pessoal | investimento | empresarial | misto)
  ├── Carrega frameworks/output-review-ready.md
  ├── Carrega frameworks/segunda-opiniao.md
  ├── Carrega framework(s) temático(s)
  │
[Análise]
  │
  ├── Produz output no esquema review-ready (8 seções)
  ├── Sinaliza ⚠️ se a análise depende de informação após VERSAO.md
  │
[Fim]
  │
  ├── Propõe reescrita dos arquivos de estado (ralph-loop)
  ├── Cada arquivo ≤ 2 KB
  └── Usuário aprova antes da reescrita ser considerada final
```

## Decisões arquiteturais importantes

### Por que markdown puro

- Portabilidade entre plataformas (Claude, ChatGPT, Copilot)
- Legível por humanos para revisão e auditoria
- Versionável em git sem fricção
- Sem dependência de runtime

### Por que memória escopada

- Privacidade por design — sem campos para dados pessoais identificáveis
- Reduz risco se o estado vazar em log de plataforma
- Força o agente a raciocinar em termos relativos (faixas, percentuais, ratios)

### Por que ralph-loop (reescrita) em vez de log

- Limites de contexto das plataformas
- Aprendizado condensado é mais útil para sessões futuras do que log linha-a-linha
- Cada arquivo cabe em qualquer janela de contexto

### Por que saída padronizada review-ready

- Permite revisão por consultor humano ou outro agente sem ler a conversa original
- Premissas explícitas tornam o raciocínio auditável
- Seção "Para o revisor" força o agente a explicitar suas próprias limitações

### Por que tributação isolada com data de verificação

- Tributação muda com frequência — isolamento facilita manutenção
- "Última verificação" visível permite ao agente sinalizar desatualização sem alucinar

## Quando adicionar um novo framework

Crie novo arquivo em `frameworks/` quando:

- Um domínio novo entra no escopo (ex: imóveis, criptoativos, derivativos)
- Um domínio existente cresce a ponto de o framework atual ficar grande demais (limite prático: ~10 KB por arquivo)
- Uma jurisdição nova de tributação entra (em `frameworks/tributacao/`)

Cada novo framework precisa de:

- Estrutura idêntica: definições → perguntas-chave → exemplos → armadilhas
- Pelo menos 2 exemplos resolvidos
- Test case correspondente em `tests/` se altera o comportamento do agente
- Atualização no `CHANGELOG.md`

## Quando NÃO adicionar um framework

Não criar:

- Framework de "previsão de mercado" — fora do escopo declarado
- Framework de "estratégias automatizadas" — fora do escopo
- Frameworks que repitam conteúdo já presente em outros — refatorar em vez de duplicar

## Compatibilidade entre plataformas

O agente foi projetado para funcionar em modo "rich" (Claude Projects) e modo "degradado" (ChatGPT, Copilot). Diferenças principais:

| Capacidade | Claude Projects | ChatGPT Custom GPT | Copilot e outros |
|---|---|---|---|
| Knowledge persistente | sim | sim | não |
| Escrita automática de estado | sim | não | não |
| Ralph-loop | semi-automático | manual | manual |
| Limite de contexto | alto | médio | menor |

A skill foi projetada com o denominador comum: **funciona em todos** com mais ou menos esforço manual.

## Limites de escala atuais

- Frameworks: cada um deve caber em ~10 KB para preservar contexto
- Estado: cada arquivo ≤ 2 KB
- Total da skill (todos os arquivos): caber confortavelmente em uma knowledge base padrão (~25 MB nas plataformas atuais não é problema)

## Evolução futura

Marcos previstos:

- v0.2: framework de criptoativos, framework de imóveis
- v0.3: mais jurisdições tributárias (Portugal, países latino-americanos)
- v0.4: runner automatizado de testes via outro agente
