# Framework — Investimentos

Use quando a análise envolve alocação de patrimônio, escolha de classe de ativo, comparação entre estratégias ou rebalanceamento.

## Classes de ativo (visão qualitativa)

### Renda fixa pós-fixada

- **Driver de retorno:** taxa de juros corrente
- **Risco principal:** crédito do emissor (se privado); reinvestimento em queda de juros
- **Liquidez:** alta (depende do produto)
- **Quando faz sentido:** reserva de emergência, parcela conservadora, regime de juros altos

### Renda fixa prefixada

- **Driver de retorno:** taxa contratada na compra
- **Risco principal:** marcação a mercado se vendido antes do vencimento; expectativa de juros pode mudar
- **Liquidez:** média
- **Quando faz sentido:** quando o usuário pode levar ao vencimento e acredita que as taxas atuais são atrativas

### Renda fixa atrelada à inflação

- **Driver de retorno:** inflação + cupom real
- **Risco principal:** marcação a mercado; expectativa de inflação muda
- **Liquidez:** média
- **Quando faz sentido:** proteção contra inflação no longo prazo; aposentadoria

### Ações (renda variável)

- **Driver de retorno:** lucro das empresas + multiplicador de mercado
- **Risco principal:** volatilidade, drawdown, risco específico de cada empresa
- **Liquidez:** alta (em empresas líquidas)
- **Quando faz sentido:** horizonte longo, capacidade de tolerar drawdown

### Fundos imobiliários

- **Driver de retorno:** renda de aluguel + valorização dos ativos imobiliários
- **Risco principal:** vacância, ciclo imobiliário, risco específico do segmento
- **Liquidez:** média
- **Quando faz sentido:** renda recorrente, diversificação

### ETFs

- **Driver de retorno:** índice de referência
- **Risco principal:** o mesmo do índice subjacente
- **Liquidez:** alta
- **Quando faz sentido:** diversificação barata e simples; substituto a fundos ativos quando o gestor não justifica taxa

### Multimercados

- **Driver de retorno:** alfa do gestor
- **Risco principal:** gestor performar abaixo de benchmark; taxas altas; iliquidez de alguns
- **Liquidez:** varia (D+30 ou mais comum)
- **Quando faz sentido:** apenas se há convicção sobre o gestor e custo justifica

### Criptoativos

- **Driver de retorno:** especulação + adoção
- **Risco principal:** volatilidade extrema, regulatório, custódia
- **Liquidez:** varia
- **Quando faz sentido:** parcela pequena para perfis arrojados que toleram perda total

### Alternativos (private equity, venture, real estate direto)

- **Driver de retorno:** valorização do ativo subjacente + alavancagem
- **Risco principal:** iliquidez total por anos, risco operacional
- **Liquidez:** baixa
- **Quando faz sentido:** patrimônio já consolidado, parcela pequena, horizonte muito longo

## Alocação de referência por perfil

Esses são pontos de partida, refinados por horizonte e objetivos.

### Conservador

- Renda fixa: 70-90%
- Renda variável (ações + FII): 10-30%
- Cripto: 0-2%
- Alternativos: 0%

### Moderado

- Renda fixa: 40-60%
- Renda variável: 30-50%
- Internacional: 5-15%
- Cripto: 0-5%
- Alternativos: 0-5%

### Arrojado

- Renda fixa: 20-40%
- Renda variável: 50-70%
- Internacional: 10-25%
- Cripto: 0-10%
- Alternativos: 0-15%

## Rebalanceamento

### Regra padrão

- Periodicidade: anual ou semestral
- Gatilho por desvio: rebalancear quando uma classe se desvia mais de 5 pontos percentuais do alvo

### Por que rebalancear

- Mantém o perfil de risco controlado (sem rebalanceamento, ações tendem a crescer em peso e levar a carteira para perfil mais arrojado que o declarado)
- Força disciplina de "vender o que subiu, comprar o que caiu"
- Reduz dependência de timing de mercado

### Quando NÃO rebalancear

- Custo fiscal de rebalanceamento alto e desvio pequeno
- Próximo a marcos de tributação (verificar regras do país, ver `frameworks/tributacao/`)
- Em situações de alta convicção temporária — registrar como exceção justificada nas premissas

## Como avaliar uma carteira existente (sem dados pessoais)

Quando o usuário traz a carteira atual, peça apenas:

- Percentual por classe de ativo (não R$ absolutos)
- Tickers ou tipos de produto (sem números de conta, sem corretora específica se isso permitir inferir patrimônio)
- Horizonte e razão de cada posição

A análise pode ser feita totalmente em percentuais e ratios. Se o usuário insistir em mandar valores absolutos, aplicar protocolo anti-dados-sensíveis do `AGENT.md`.

## Gatilho de visualização

Análises de investimento devem renderizar gráficos automaticamente conforme `frameworks/visualizacao.md`. Antes de finalizar a saída, percorra este checklist:

- [ ] Seção 3 tem 3+ dimensões com score 1-5? → renderizar **V1 (radar)** dentro da Seção 3
- [ ] Há dados de alocação (input ou recomendação) com 2+ categorias somando ≈100%? → renderizar **V2 (pizza/donut)** no local apropriado (Seção 2 se input, Seção 6 se output)
- [ ] Seção 5 compara 3+ opções com risco e retorno qualitativos? → renderizar **V3 (quadrante)** dentro da Seção 5

Quando um pré-requisito não for satisfeito, **não** renderizar aquele gráfico e registrar a razão na Seção 7 (sub-item "Dados ausentes que mudariam a análise" ou "Onde discordo de mim mesmo").

Quando a plataforma corrente não suportar o tipo de gráfico exigido (ver capability matrix em `frameworks/visualizacao.md`), declarar abertamente em vez de fingir.

## Decisões comuns e como tratá-las

### "Devo investir tudo de uma vez ou aos poucos?"

- Matemática esperada: lump sum costuma performar melhor em média (mercados sobem na maior parte do tempo)
- Mas: aporte gradual reduz arrependimento se o mercado cair logo após o aporte
- Recomendação prática para horizonte longo: lump sum se a quantia é pequena relativa ao patrimônio; aporte gradual em 3-12 meses se é grande

### "Devo concentrar em poucas posições ou diversificar muito?"

- Concentração funciona se há convicção e capacidade de aprofundar análise
- Diversificação reduz drawdown ao custo de retorno esperado
- Para a maioria dos investidores não-profissionais, diversificação ampla via ETFs é mais robusta

### "Devo tentar bater o mercado escolhendo ações?"

- Evidência empírica: a maioria dos gestores profissionais não bate o índice ao longo de décadas
- Stock picking exige tempo, processo e estômago
- Apresentar como opção válida apenas se o usuário demonstrou ter os três acima

## Exemplo resolvido 1 — alocação inicial para moderado, horizonte longo

**Situação:** Usuário moderado, horizonte 10+ anos, BR, sem dependentes, reserva já constituída.

- 50% renda fixa (mix de pós-fixado, prefixado curto e atrelada à inflação)
- 35% ações (15% via ETF amplo do índice doméstico, 10% ações específicas com tese clara, 10% ações no exterior via ETF)
- 10% fundos imobiliários (diversificados em segmentos)
- 5% reserva para oportunidades (em pós-fixado)
- Rebalanceamento anual

## Exemplo resolvido 2 — carteira concentrada em ações domésticas

**Situação:** Usuário moderado, 80% em ações domésticas individuais, 20% em renda fixa.

- Diagnóstico: alocação atual está bem fora do perfil moderado declarado
- Recomendação: reduzir ações para 35-50%, diluir entre ETF do índice (parte da posição), ações no exterior, fundos imobiliários
- Implementação: rebalanceamento em 2-3 movimentos para diluir impacto fiscal
- Sinalização: na seção 4, apontar que o usuário pode ter perfil real mais arrojado que o declarado — vale revisitar o perfil
