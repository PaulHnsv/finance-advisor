# Framework — Finanças pessoais

Use este framework quando o pedido do usuário envolve organização do orçamento, reserva, dívidas, seguros essenciais ou primeiros investimentos.

## Ordem de prioridade canônica

Quando o usuário pergunta "por onde começar" ou "o que fazer com sobra de dinheiro", a ordem padrão é:

1. **Reserva de emergência mínima** — meses de despesa essencial em liquidez imediata
2. **Quitação de dívida cara** — qualquer dívida cuja taxa de juros supere a rentabilidade líquida da renda fixa pós-imposto
3. **Seguros essenciais** — vida (se há dependentes), saúde, eventualmente invalidez
4. **Reserva de emergência completa** — completar até 6-12 meses conforme estabilidade da renda
5. **Investimento de longo prazo** — alocação por perfil e horizonte
6. **Objetivos discricionários** — viagens, hobbies, consumo planejado

Essa ordem pode ser ajustada caso o usuário descreva uma situação específica, mas a justificativa deve aparecer nas premissas (Seção 2 do output).

## Diretrizes de reserva de emergência

Tamanho recomendado por estabilidade de renda (em meses de despesa essencial, não de receita):

- Alta estabilidade (funcionário público, CLT em setor estável): 3-6 meses
- Média estabilidade (CLT em setor cíclico, PJ com contratos longos): 6-9 meses
- Baixa estabilidade (autônomo, comissionado, renda variável): 9-12 meses ou mais

A reserva precisa de liquidez imediata (idealmente D+0, no máximo D+1) e baixíssimo risco de crédito. Produtos típicos por jurisdição entram em `frameworks/investimentos.md`.

## Framework 50/30/20 (e suas limitações)

A regra clássica sugere alocar a renda líquida em:

- 50% necessidades (moradia, alimentação, transporte essencial)
- 30% desejos (lazer, hobbies, consumo discricionário)
- 20% poupança e investimento

**Limitações importantes:**

- Em jurisdições com alto custo de vida, o "50%" pode ser irreal e empurrar o usuário a comprometer o "20%"
- O 20% é mínimo para quem está começando; pessoas próximas da aposentadoria ou em recuperação financeira podem precisar de 30-40%
- A regra ignora dívidas existentes — se há dívida cara, parte do "20%" vai para quitação antes de investimento

Use o 50/30/20 como ponto de partida pedagógico, não como prescrição rígida.

## Métodos de quitação de dívida

### Avalanche (mais eficiente economicamente)

Pague o mínimo de todas as dívidas e direcione qualquer excedente para a dívida com **maior taxa de juros**. Quando ela termina, parte para a segunda mais cara.

- **Vantagem:** menor custo total de juros
- **Desvantagem:** pode ser psicologicamente difícil se a dívida mais cara também for a maior em valor

### Bola de neve (mais sustentável psicologicamente)

Pague o mínimo de todas as dívidas e direcione qualquer excedente para a dívida de **menor saldo**. Quando ela termina, parte para a segunda menor.

- **Vantagem:** quitações rápidas geram motivação
- **Desvantagem:** custo total maior em juros

### Quando recomendar cada um

- Avalanche: usuário com disciplina e perfil analítico
- Bola de neve: usuário que precisa ver progresso rápido para não desistir

Independente do método, **nunca** recomende contrair nova dívida cara para "ganhar com investimento" — a matemática quase nunca fecha após impostos e risco.

## Investimento antes de quitar dívida — quando faz sentido

Em geral, quitar dívida cara primeiro. Mas há exceções:

- **Matching de empregador em previdência** — se a empresa dobra contribuição até um limite, é "retorno" garantido que pode superar juros da dívida
- **Reserva mínima inexistente** — sem reserva, qualquer imprevisto vira nova dívida; manter um colchão mínimo antes de quitação total
- **Dívida com taxa menor que renda fixa líquida** — raras (financiamento subsidiado de imóvel, em alguns casos)

## Sinais de alerta na conversa

Quando o usuário traz situação financeira pessoal, fique atento a:

- Dívida rotativa de cartão sendo paga só do mínimo
- Ausência total de reserva e renda instável
- "Saída para sair de uma dívida tomando outra"
- Compromisso financeiro de longo prazo (financiamento de imóvel) sem reserva
- Investimento alavancado proposto enquanto há dívida cara não quitada

Em qualquer desses casos, sua análise deve apontar isso na Seção 4 e na Seção 6.

## Exemplo resolvido 1 — pessoa começando

**Situação:** Sem reserva, sem dívida cara, sobra mensal de ~20% da renda, perfil moderado, horizonte longo.

- **Recomendação 1:** Construir reserva de 6 meses de despesa em produto de liquidez imediata e baixo risco — confiança alta.
- **Recomendação 2:** Após reserva, iniciar aporte mensal em alocação moderada conforme `frameworks/investimentos.md` — confiança alta.
- **Recomendação 3:** Avaliar necessidade de seguro de vida só se houver dependentes ou compromisso financeiro de longo prazo — confiança média.

## Exemplo resolvido 2 — pessoa com dívida cara

**Situação:** Tem reserva mínima de 1 mês, dívida de cartão rotativo, sobra mensal pequena.

- **Recomendação 1:** Pausar aportes em qualquer investimento que não seja matching de empregador — confiança alta.
- **Recomendação 2:** Aplicar todo excedente na quitação da dívida cara (método avalanche se há mais de uma) — confiança alta.
- **Recomendação 3:** Reavaliar orçamento para tentar elevar o excedente; eventualmente renegociar a dívida — confiança média.
- **Recomendação 4:** Após quitação, completar a reserva de emergência até atingir 6 meses antes de retomar investimento de longo prazo — confiança alta.
