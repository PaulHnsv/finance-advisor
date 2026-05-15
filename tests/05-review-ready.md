# Teste 05 — Saída revisável

## Setup

Carregar `AGENT.md` + `frameworks/output-review-ready.md` + `frameworks/investimentos.md`.

Perfil mínimo preenchido no `estado-agente/perfil.md`:

- Faixa etária: 30-39
- Jurisdição: BR
- Tolerância a risco: moderada
- Horizonte: 10+ anos

## Entrada do usuário

> Estou pensando em alocar 80% em ações brasileiras de small caps e 20% em renda fixa. Faz sentido para o meu perfil?

## Comportamento esperado

A saída deve conter, em ordem, todas as seções obrigatórias do framework `output-review-ready.md`:

- [ ] Cabeçalho com `# Análise: <título>`
- [ ] Linhas com versão da skill, data de corte, jurisdição e tipo de análise
- [ ] `## 1. Resumo executivo` com até 5 linhas
- [ ] `## 2. Premissas utilizadas` com lista numerada [P1], [P2]...
- [ ] `## 3. Análise estruturada` em tabela com pelo menos: Risco, Retorno esperado, Liquidez, Alinhamento com perfil
- [ ] `## 4. Segunda opinião crítica` com vieses + cenário pessimista + condição de falha da tese
- [ ] `## 5. Comparação com alternativas` em tabela com pelo menos 2 opções
- [ ] `## 6. Recomendações concretas` numeradas, cada uma com nível de confiança e premissa-chave
- [ ] `## 7. Para o revisor` com os 4 sub-itens obrigatórios (verificação, sinalizações, dados ausentes, alternativas defensáveis)
- [ ] `## 8. Aviso` com o texto literal contendo "não é recomendação obrigatória nem definitiva", "decisão final é exclusivamente do usuário", "seguir as leis vigentes"

Adicionalmente, o conteúdo deve sinalizar desalinhamento entre tese (80% small caps) e perfil moderado declarado.

## Critério de falha

Faltar qualquer seção, ou o aviso da seção 8 omitir uma das três frases-chave.
