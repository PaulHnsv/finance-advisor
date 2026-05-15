# finance-advisor

Agente persistente de **avaliação crítica de estratégias financeiras**, portátil entre Claude, ChatGPT e Copilot.

[![Licença: MIT](https://img.shields.io/badge/licen%C3%A7a-MIT-blue.svg)](LICENSE)
[![Versão](https://img.shields.io/badge/vers%C3%A3o-0.1.0-orange.svg)](VERSAO.md)

> **Aviso:** este projeto é educacional. **Não constitui recomendação financeira regulada, não é definitivo nem obrigatório.** A decisão final é sempre do usuário. Sempre seguir as leis vigentes. Para decisões com impacto significativo, consulte assessor financeiro registrado e contador habilitado.

## O que é

Um conjunto de arquivos markdown que, quando instalados em uma plataforma de IA (Claude Projects, ChatGPT Custom GPT, Copilot), transforma o assistente em um "consultor crítico" que:

- Avalia teses e estratégias financeiras de forma estruturada
- Mantém memória persistente do perfil e da situação do usuário, **sem armazenar dados pessoais identificáveis**
- Produz saídas padronizadas para serem **revisadas por outros consultores** (humanos ou agentes)
- Sinaliza proativamente quando alguma informação pode estar desatualizada
- Aplica o "padrão ralph-loop" para manter estado compacto entre sessões

## O que NÃO é

- Não é um assessor financeiro registrado
- Não é uma fonte definitiva ou obrigatória
- Não dá sinais de operação, previsões de preço, ou recomendações de timing
- Não substitui contador habilitado para temas tributários
- Não executa ordens nem se conecta a corretoras

## Instalação rápida

| Plataforma | Recomendação | Guia |
|---|---|---|
| Claude (Projects / Cowork) | **Recomendado** — ralph-loop semi-automático | [Guia](guias-instalacao/claude.md) |
| ChatGPT (Custom GPT) | Funciona com ralph-loop manual | [Guia](guias-instalacao/chatgpt.md) |
| Microsoft Copilot e outros | Modo degradado — operacional, mas mais trabalho manual | [Guia](guias-instalacao/copilot.md) |

**Antes de tudo:** leia o [guia de segurança do usuário](guias-instalacao/seguranca-do-usuario.md) — orienta sobre o que **não enviar** ao agente para preservar sua privacidade.

## Como funciona

A cada sessão, o agente:

1. **Lê o estado persistente** (perfil, situação relativa, teses ativas, decisões anteriores)
2. **Verifica dados sensíveis** na entrada do usuário e recusa caso encontre
3. **Classifica o pedido** (pessoal, investimento, empresarial ou misto) e carrega frameworks pertinentes
4. **Produz análise estruturada** com 8 seções obrigatórias, incluindo segunda opinião crítica e seção "Para o revisor"
5. **Sinaliza** quando a análise depende de informação posterior à data de corte
6. **Propõe reescrita do estado** ao final da sessão (ralph-loop), substituindo em vez de anexar

## Estrutura do repositório

```
finance-advisor/
├── AGENT.md                          ← Definição do agente
├── VERSAO.md                         ← Versão e data de corte
├── frameworks/                       ← Conhecimento de domínio
│   ├── perfil-investidor.md
│   ├── analise-risco.md
│   ├── financas-pessoais.md
│   ├── investimentos.md
│   ├── valuation-acoes.md
│   ├── financas-empresariais.md
│   ├── segunda-opiniao.md
│   ├── output-review-ready.md
│   └── tributacao/
│       ├── BR.md
│       ├── US.md
│       └── template.md
├── estado-agente/                    ← Templates de memória persistente
├── guias-instalacao/                 ← Setup por plataforma
├── tests/                            ← Casos de teste manuais
└── docs/                             ← Documentação para contribuintes
```

## Documentação

- [Arquitetura](docs/arquitetura.md) — visão completa para contribuintes
- [Princípios](docs/principios.md) — fundamentos que governam o design
- [Glossário](docs/glossario.md) — termos do projeto e termos financeiros

## Status e roadmap

- **v0.1.0 (atual):** núcleo completo — agente, 7 frameworks, tributação BR e US essencial, guias para 3 plataformas, 6 test cases
- **v0.2:** framework de criptoativos, framework de imóveis, mais exemplos
- **v0.3:** jurisdições adicionais (Portugal, outros países latino-americanos)
- **v0.4:** runner automatizado de testes

## Contribuindo

Veja [CONTRIBUTING.md](CONTRIBUTING.md) e [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

Tipos de contribuição esperados:

- Atualizações regulatórias e tributárias (usar template `framework-update`)
- Novos frameworks (usar template `new-framework`)
- Correções de bug com test case (usar template `bug`)
- Melhorias nos guias de instalação

## Licença

MIT — ver [LICENSE](LICENSE).
