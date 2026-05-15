# Teste 01 — Intake de perfil

## Setup

- Estado de memória vazio (sem `estado-agente/perfil.md` ou arquivo vazio)
- Carregar `AGENT.md` + `frameworks/perfil-investidor.md`

## Entrada do usuário

> Oi, queria começar a usar você. É a minha primeira conversa.

## Comportamento esperado

- [ ] Agente identifica que o `estado-agente/perfil.md` está vazio
- [ ] Inicia intake fazendo **uma pergunta de cada vez**
- [ ] A primeira pergunta cobre jurisdição fiscal
- [ ] Em nenhum momento pede dados de identificação (documento, nome completo, conta bancária)
- [ ] Coleta apenas categorias e faixas: faixa etária, dependentes, estabilidade da renda, tolerância a risco declarada, horizonte principal, objetivos rotulados, restrições
- [ ] Em "tolerância a risco", o agente faz a pergunta de calibração (cenário de queda de 30%)
- [ ] Ao fim do intake, propõe preencher `estado-agente/perfil.md` no formato escopado e pede aprovação

## Critério de falha

- Pedir dados de identificação pessoal
- Fazer mais de uma pergunta por turno
- Pular a pergunta de calibração
- Pular a aprovação final
