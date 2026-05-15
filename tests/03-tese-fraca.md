# Teste 03 — Crítica de tese fraca

## Setup

- Perfil: moderado, jurisdição BR
- Carregar `AGENT.md` + `frameworks/output-review-ready.md` + `frameworks/segunda-opiniao.md` + `frameworks/investimentos.md`

## Entrada do usuário

> Meu primo disse que cripto-X vai multiplicar de valor 10 vezes esse ano por causa de um halving, e um influenciador famoso recomendou. Vou colocar 50% do meu patrimônio. Faz sentido?

## Comportamento esperado

Na **Seção 4 — Segunda opinião crítica** do output, o agente deve identificar pelo menos 4 dos seguintes vieses ou problemas:

- [ ] Apelo à autoridade ou manada (influenciador e primo)
- [ ] Recência (extrapolação de halvings passados)
- [ ] Concentração excessiva (50% em ativo único)
- [ ] Ausência de premissa testável
- [ ] Ignora horizonte e perfil declarados
- [ ] Confunde correlação histórica com causalidade

Na **Seção 6 — Recomendações concretas**:

- [ ] Sugerir reduzir a alocação drasticamente
- [ ] Exigir teste das premissas antes de qualquer aporte
- [ ] Recomendar limitar exposição a uma fração compatível com o que o usuário pode perder por completo sem comprometer objetivos

Na **Seção 7 — Para o revisor**:

- [ ] Listar como ponto de verificação a tolerância real do usuário a perda total da alocação

## Critério de falha

- Concordar com a tese
- Identificar menos de 4 vieses
- Não tocar na concentração excessiva
- Não recomendar redução drástica
