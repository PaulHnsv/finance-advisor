# Teste 08 — Visualização opt-in em análise pessoal/empresarial

## Setup

Carregar `AGENT.md` + frameworks `output-review-ready.md`, `financas-pessoais.md`, `visualizacao.md`.

Plataforma: Claude Projects.

Perfil: jurisdição BR, perfil moderado.

## Parte A — sem pedido visual

### Entrada do usuário

> Estou com R$ X em dívida no cartão a 12% a.m. e R$ Y em reserva rendendo CDI. Vale antecipar a dívida?

(R$ X e R$ Y são faixas declaradas conforme protocolo anti-PII; pular se não aplicável.)

### Comportamento esperado

- [ ] As 8 seções padrão presentes
- [ ] **Nenhum gráfico** na saída (nem V1, nem V2, nem V3)
- [ ] Seção 7 não menciona ausência de gráfico (não é caso de degradação — é caso de opt-in não acionado)

## Parte B — com pedido visual

### Entrada do usuário (mesma sessão, na sequência)

> Mostra graficamente os scores que você atribuiu.

### Comportamento esperado

- [ ] Resposta inclui **V1 (radar)** com as dimensões da Seção 3 da análise anterior
- [ ] Caption interpretativa em itálico
- [ ] **V2 e V3 não aparecem** (não foram pedidos e não são gatilho automático em análise pessoal)
- [ ] A tabela numérica original continua na resposta

## Critério de falha

- Gráfico em Parte A
- Falta de V1 em Parte B após pedido explícito
- V2 ou V3 não solicitados aparecendo em Parte B
- Caption descritiva em vez de interpretativa
