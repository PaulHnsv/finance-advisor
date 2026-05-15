# Framework — Valuation qualitativa de ações

Use quando o usuário pede análise de uma ação específica, comparação entre empresas, ou avaliação de uma tese de investimento em renda variável.

**Aviso de escopo:** este framework cobre análise **qualitativa** e múltiplos simples. Não é um substituto para modelagem financeira profissional. Recomendações de compra/venda específicas não fazem parte do escopo deste agente — apenas avaliação crítica das premissas que o usuário trouxe.

## Múltiplos básicos e quando usar cada um

### P/L (preço sobre lucro)

- **O que mede:** quanto o mercado paga por cada unidade de lucro anual
- **Quando faz sentido:** empresas estáveis e lucrativas
- **Quando engana:** lucro distorcido por evento extraordinário; lucro contábil ≠ caixa; empresas em prejuízo; empresas com lucro decrescente

### P/VP (preço sobre valor patrimonial)

- **O que mede:** quanto o mercado paga sobre o patrimônio líquido contábil
- **Quando faz sentido:** bancos, seguradoras, holdings cujo valor é fortemente ligado ao ativo
- **Quando engana:** empresas de serviços ou marca onde o valor está fora do balanço

### EV/EBITDA

- **O que mede:** valor da firma (capital próprio + dívida líquida) sobre lucro operacional antes de juros, impostos e depreciação
- **Quando faz sentido:** comparações entre empresas com estruturas de capital diferentes; setores com alta depreciação
- **Quando engana:** EBITDA pode esconder gastos reais de capital (intensivos em CAPEX); manipulação contábil em depreciação

### Dividend yield

- **O que mede:** rendimento de dividendos sobre o preço atual
- **Quando faz sentido:** empresas maduras com pagamento estável
- **Quando engana:** dividendo alto pode estar prestes a ser cortado; preço caiu por motivo ruim; payout insustentável

## DCF simplificado (valor da firma por fluxo de caixa)

Versão "back-of-envelope" — não substitui modelagem profissional, mas serve para checar a ordem de grandeza:

1. Estimar fluxo de caixa livre anual normalizado (lucro operacional - imposto - capex de manutenção + depreciação)
2. Aplicar taxa de crescimento conservadora (no máximo crescimento do PIB de longo prazo para empresas maduras)
3. Aplicar taxa de desconto compatível com risco (juros longos + prêmio de risco)
4. Adicionar valor terminal (perpetuidade com crescimento baixo)
5. Subtrair dívida líquida, dividir pelo número de ações

**Limitações fundamentais:**

- Pequena variação na taxa de crescimento ou desconto muda o resultado dramaticamente
- "Garbage in, garbage out" — premissas otimistas geram valores otimistas
- Sempre comparar resultado do DCF com o que está implícito no preço de mercado e perguntar: "quais premissas o mercado está adotando?"

## Qualidade do negócio

Múltiplos baixos podem ser oportunidade ou armadilha. A diferença está na qualidade do negócio.

### Sinais positivos

- Margem operacional consistente acima da média do setor
- ROIC (retorno sobre capital investido) consistente acima do custo de capital
- Vantagem competitiva duradoura (marca, custo, rede, switching cost, regulação favorável)
- Reinvestimento gerando crescimento (capex que aumenta lucro futuro)
- Governança alinhada com minoritários

### Sinais negativos

- Margem cadente sem causa cíclica clara
- ROIC abaixo do custo de capital há vários anos
- Dependência de cliente único ou regulação volátil
- Aquisições sucessivas mascarando crescimento orgânico fraco
- Histórico de diluição agressiva ou conflito com minoritários

## Margem de segurança

Mesmo com análise rigorosa, premissas erram. A margem de segurança é a diferença entre o valor estimado e o preço pago. Quanto maior a incerteza nas premissas, maior deveria ser a margem exigida.

Heurística qualitativa:

- Empresa muito previsível e de alta qualidade: margem mínima de 15-25%
- Empresa cíclica ou de qualidade média: margem mínima de 30-40%
- Empresa em recuperação ou setor em transformação: margem mínima de 40-50%

## Limitações declaradas do framework

Setores em que valuation simples geralmente falha:

- **Financeiro (bancos, seguradoras)** — alavancagem natural distorce métricas convencionais; usar P/VP ajustado e ROE
- **Empresas em prejuízo** — múltiplos baseados em lucro não funcionam; analisar caminho até lucratividade
- **Alta intensidade de capital ou commodities** — ciclo distorce métricas anualizadas; olhar média de ciclo
- **Empresas de crescimento muito rápido** — múltiplos altos podem fazer sentido se crescimento se sustenta; mas a maioria dos crescimentos rápidos desacelera mais cedo que o esperado

## Exemplo resolvido 1 — empresa de qualidade aparentemente cara

**Situação:** Usuário trouxe uma empresa com ROIC consistente acima de 20%, margem operacional alta, P/L atual 30. "Está cara?"

- Diagnóstico: P/L 30 isolado não diz se está cara — depende do crescimento e qualidade. Para uma empresa que cresce lucros 15% a.a. sustentavelmente, P/L 30 pode ser razoável.
- Verificação: o crescimento histórico se sustenta nos próximos 5-10 anos? Há sinais de desaceleração?
- DCF de checagem: com crescimento 12%, taxa de desconto 12%, ROIC 20%, valor implícito pode estar próximo ou abaixo do preço atual
- Veredito sugerido: não é "barata" mas a qualidade pode justificar — depende de quanto se acredita no crescimento futuro
- Sinalização: aviso explícito de que múltiplos altos amplificam o risco se o crescimento decepcionar

## Exemplo resolvido 2 — empresa barata com sinais ruins

**Situação:** Empresa com P/L 5, P/VP 0.6, dividend yield 12%. "Parece barata. Compro?"

- Diagnóstico: barato é resultado, não causa. Por que está barata?
- Verificações obrigatórias: margens últimos 5 anos (caindo?), endividamento (aumentando?), ROIC vs custo de capital, qualidade do payout (sustentável?)
- Cenário pessimista: a empresa pode estar barata porque o mercado precifica deterioração estrutural já visível; dividendo 12% pode ser corte iminente
- Veredito sugerido: não basta o preço — entender por que está descontada. "Value trap" é o caso comum.
- Sinalização: o agente deve sinalizar essa armadilha e exigir investigação antes de qualquer recomendação

## Armadilhas comuns

- Comparar múltiplo de uma empresa com média de setores diferentes
- Usar EBITDA sem ajustar para capex
- Tomar lucro de um ano único como base
- Ignorar dívida e endividamento ao olhar P/L
- Confundir "muito caiu" com "barata"
- Acreditar que a empresa que você conhece bem como cliente também é boa como investimento
