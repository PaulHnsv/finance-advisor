# Glossário

Termos usados nos frameworks. Referência única para que os arquivos não dupliquem definições.

## Termos do projeto

**AGENT.md** — Arquivo principal de definição do agente, contém identidade e protocolos. Carregado como system prompt da plataforma.

**Framework** — Arquivo em `frameworks/` com conhecimento de domínio (perfil, risco, valuation, etc.).

**Estado-agente** — Conjunto de 4 arquivos persistentes que guardam memória do usuário entre sessões, em formato escopado.

**Ralph-loop** — Padrão de manutenção do estado: a cada sessão, o agente reescreve (substitui) os arquivos de estado mantendo só o relevante. Não é log incremental.

**Review-ready** — Formato padronizado de saída com 8 seções obrigatórias, projetado para revisão por consultor humano ou outro agente.

**PII** — Personally Identifiable Information; dados pessoais identificáveis. O agente é projetado para não armazená-los.

**Sinalização proativa** — Quando o agente identifica que sua análise pode estar desatualizada ou imprecisa, emite ⚠️ explicitamente em vez de calar.

## Termos financeiros gerais

**Drawdown** — Maior queda percentual de pico a vale em um ativo ou carteira. Métrica de risco que captura a dor de carregar a posição.

**Volatilidade** — Magnitude das oscilações típicas de um ativo. Métrica quantitativa, geralmente expressa como desvio-padrão dos retornos.

**Correlação** — Quão dois ativos se movem juntos. Diversificação real requer correlação baixa em momentos de stress.

**VaR (Value at Risk)** — Estimativa da perda máxima esperada com determinada probabilidade em um horizonte definido. Conceito útil; cálculo exato fora do escopo.

**Liquidez** — Capacidade de sair da posição sem grande deságio. Liquidez D+0 significa resgate no mesmo dia; D+30 significa 30 dias úteis.

**Risco de contraparte** — Risco do emissor ou da instituição não honrar o compromisso.

**Risco regulatório** — Risco de mudança de lei afetar o produto.

**Margem de segurança** — Diferença entre valor estimado e preço pago em uma análise de valuation. Quanto maior a incerteza, maior a margem requerida.

## Múltiplos de valuation

**P/L (Preço/Lucro)** — Preço da ação dividido pelo lucro por ação. Quanto o mercado paga por unidade de lucro anual.

**P/VP (Preço/Valor Patrimonial)** — Preço da ação dividido pelo valor patrimonial por ação.

**EV/EBITDA** — Enterprise Value dividido por EBITDA. EV = valor de mercado + dívida líquida.

**EBITDA** — Earnings Before Interest, Taxes, Depreciation and Amortization. Lucro antes de juros, impostos, depreciação e amortização.

**Dividend Yield (DY)** — Dividendo anual dividido pelo preço atual.

**ROIC** — Return on Invested Capital. Retorno sobre o capital investido na operação.

**ROE** — Return on Equity. Retorno sobre o patrimônio líquido.

## Análise de projeto

**Payback** — Tempo até recuperar o investimento inicial via fluxos esperados.

**TIR** — Taxa Interna de Retorno. Taxa que iguala valor presente líquido dos fluxos a zero.

**VPL** — Valor Presente Líquido. Soma dos fluxos descontados menos investimento inicial.

**DCF** — Discounted Cash Flow. Método de valuation por desconto de fluxos de caixa futuros.

**FCO** — Fluxo de Caixa Operacional. Caixa gerado pela operação principal da empresa.

**FCL** — Fluxo de Caixa Livre. FCO menos capex de manutenção.

## Finanças pessoais

**Reserva de emergência** — Reserva em liquidez imediata para cobrir despesas em caso de imprevisto. Sugerido: 3-12 meses de despesa, conforme estabilidade da renda.

**50/30/20** — Regra de bolso para alocação da renda: 50% necessidades, 30% desejos, 20% poupança. Ponto de partida, não prescrição rígida.

**FIRE (Financial Independence, Retire Early)** — Filosofia de independência financeira focada em alta taxa de poupança para acumular patrimônio que sustenta gastos via retorno.

**Método avalanche** — Estratégia de quitação de dívida priorizando a de maior taxa de juros.

**Método bola de neve** — Estratégia de quitação de dívida priorizando a de menor saldo.

**Rebalanceamento** — Ato de reajustar a carteira aos pesos-alvo definidos por perfil. Regra padrão: anual ou semestral, ou por gatilho de desvio > 5pp.

## Tributação Brasil (resumo)

**CDB** — Certificado de Depósito Bancário. Renda fixa privada emitida por bancos.

**LCI / LCA** — Letras de Crédito Imobiliário / do Agronegócio. Isentos de IR para pessoa física.

**CRI / CRA** — Certificados de Recebíveis Imobiliários / do Agronegócio. Isentos de IR para pessoa física.

**FII** — Fundo de Investimento Imobiliário. Distribuição de rendimentos isenta de IR sob certas condições.

**DARF** — Documento de Arrecadação de Receitas Federais. Usado para recolher IR de ganho de capital em renda variável.

**Dedo-duro** — Retenção de 1% (operações comuns) ou 0,005% (ETFs) na fonte como antecipação de imposto.

## Outros

**Halving** — Evento em alguns criptoativos onde a recompensa por mineração é cortada pela metade. Usado em narrativas especulativas sobre preço futuro.

**Day trade** — Compra e venda do mesmo ativo no mesmo dia. Tributação separada (alíquota mais alta em vários países).

**Wash sale** — Regra (EUA) que impede o reconhecimento fiscal de prejuízo quando há recompra do mesmo ativo em curto prazo.

**MPT (Modern Portfolio Theory)** — Teoria de Markowitz sobre alocação ótima de carteiras. O agente usa conceitos qualitativos derivados, não a implementação matemática.

**Lump sum** — Aporte único de grande valor, em oposição a aporte gradual ao longo do tempo.
