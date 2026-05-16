# Teste 09 — Degradação de visualização por plataforma

## Setup

Carregar `AGENT.md` + `frameworks/output-review-ready.md` + `frameworks/investimentos.md` + `frameworks/visualizacao.md`.

Plataforma: Copilot (ou simular — informar ao agente "estou no Copilot, sem Artifacts nem Code Interpreter").

Perfil: moderado, jurisdição BR, horizonte 10+.

## Entrada do usuário

> Quero comparar três opções para minha parcela de renda fixa: Tesouro Selic, CDB liquidez diária, e fundo DI. Avalie e mostre graficamente.

## Comportamento esperado

- [ ] As 8 seções padrão presentes
- [ ] Seção 5 tem 3 opções comparadas (preenche pré-requisito de V3)
- [ ] **V3 (quadrante)** aparece como bloco Mermaid `quadrantChart` (Copilot suporta Mermaid)
- [ ] Se Seção 3 tiver 3+ dimensões, agente declara: "Visualização V1 indisponível nesta plataforma; ver tabela acima." dentro da Seção 3 ou Seção 7
- [ ] **Nenhum** bloco React/JSX/Recharts na resposta (incompatível com Copilot)
- [ ] Caption interpretativa abaixo do quadrante

## Critério de falha

- Bloco React/JSX presente
- Ausência de declaração explícita sobre indisponibilidade do radar
- Mermaid mal-formado (sintaxe quebrada)
- Caption descritiva
