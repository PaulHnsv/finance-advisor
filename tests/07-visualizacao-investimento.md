# Teste 07 — Visualização em análise de investimento

## Setup

Carregar `AGENT.md` + `frameworks/output-review-ready.md` + `frameworks/investimentos.md` + `frameworks/visualizacao.md`.

Plataforma: Claude Projects (Artifacts disponíveis).

Perfil mínimo preenchido em `estado-agente/perfil.md`:

- Faixa etária: 30-39
- Jurisdição: BR
- Tolerância a risco: moderada
- Horizonte: 10+ anos

## Entrada do usuário

> Estou montando uma carteira: 40% renda fixa pós-fixada, 30% ETF amplo BR, 15% ETF S&P 500, 10% FIIs, 5% cripto. Compare contra a alocação de referência para moderado e me diga onde estou desalinhado.

## Comportamento esperado

A saída deve conter:

- [ ] Cabeçalho padrão + as 8 seções obrigatórias do `output-review-ready.md`
- [ ] Dentro da **Seção 3**, após a tabela de scores, um **radar (V1)** renderizado como Artifact React (Recharts `<RadarChart>`), cobrindo no mínimo Risco / Retorno / Liquidez / Alinhamento
- [ ] Caption do radar em itálico abaixo, **interpretativa** (descreve insight, não eixo)
- [ ] Dentro da **Seção 6**, ou após premissa de composição na Seção 2, uma **pizza/donut (V2)** Recharts com 5 fatias (rendaFixa/ETF-BR/ETF-US/FII/cripto) usando a paleta determinística do framework
- [ ] Caption interpretativa da pizza
- [ ] **Nenhum valor absoluto** em qualquer gráfico (só percentuais ou scores)
- [ ] A tabela numérica original da Seção 3 continua presente (gráfico é complementar)
- [ ] Seção 5 com ≤2 alternativas → **V3 não aparece** (pré-requisito 3+ opções)

## Critério de falha

- Falta de V1 ou V2 quando os pré-requisitos estão satisfeitos
- Caption descritiva ("Gráfico de scores em 5 dimensões.") em vez de interpretativa
- Qualquer valor em R$/USD absoluto dentro do gráfico
- Tabela da Seção 3 substituída em vez de complementada
- V3 aparece com apenas 2 alternativas comparadas
