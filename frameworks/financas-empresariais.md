# Framework — Finanças empresariais

Use quando o pedido envolve análise de demonstrativos contábeis, viabilidade de projeto, ou avaliação de empresa em contexto operacional (não como ação cotada).

## Leitura da Demonstração de Resultado (DRE)

A DRE responde "a empresa teve lucro no período?". Leia de cima para baixo procurando:

- **Receita líquida** — cresceu vs período anterior? Crescimento é orgânico ou via aquisição?
- **Custo dos produtos/serviços vendidos** — proporção da receita está estável, melhorando ou piorando?
- **Lucro bruto e margem bruta** — qualquer queda relevante exige explicação
- **Despesas operacionais** — comerciais, gerais e administrativas; cresceram mais ou menos que a receita?
- **EBITDA / lucro operacional** — métrica de geração operacional antes de financeiro e impostos
- **Resultado financeiro** — empresa muito alavancada tem aqui um peso grande
- **Imposto de renda e contribuições** — alíquota efetiva está coerente com o regime tributário?
- **Lucro líquido** — bottom line; comparar com expectativa e tendência histórica

### Perguntas-chave a fazer

- Receita está crescendo ou caindo? Por qual razão?
- Margem está expandindo ou contraindo? Por quê?
- Há itens não recorrentes que distorcem o resultado do período?
- O lucro contábil bate com a geração de caixa? (cruza com fluxo de caixa)

## Leitura do Balanço Patrimonial

O balanço responde "qual a estrutura patrimonial da empresa em determinado momento?".

### Ativo

- **Circulante** — caixa, contas a receber, estoque, ativos esperados em 12 meses
- **Não circulante** — investimentos de longo prazo, imobilizado, intangíveis

### Passivo

- **Circulante** — obrigações em 12 meses (fornecedores, dívida de curto prazo, salários)
- **Não circulante** — dívida de longo prazo, obrigações de longa duração
- **Patrimônio líquido** — capital + reservas + lucros acumulados

### Indicadores derivados

- **Liquidez corrente** = ativo circulante / passivo circulante (> 1 sugere capacidade de honrar obrigações curtas)
- **Endividamento** = dívida total / patrimônio líquido (alto pode amplificar lucros e perdas)
- **Dívida líquida / EBITDA** — quantos anos de EBITDA atual seriam necessários para zerar a dívida? Em geral, > 3 começa a ser preocupante; depende do setor

### Sinais de alerta no balanço

- Contas a receber crescendo mais rápido que receita (problema de inadimplência?)
- Estoque crescendo mais rápido que vendas (obsolescência iminente?)
- Endividamento crescente sem crescimento proporcional de receita ou EBITDA
- Reduções sucessivas de patrimônio líquido por prejuízos ou recompras agressivas

## Leitura do Fluxo de Caixa

O fluxo de caixa responde "o que aconteceu com o caixa da empresa no período?". É a peça mais difícil de manipular contabilmente, e por isso a mais importante.

### Três blocos

- **Fluxo de caixa operacional (FCO)** — caixa gerado pela operação principal
- **Fluxo de caixa de investimento** — capex, aquisições, vendas de ativos
- **Fluxo de caixa de financiamento** — dívida, dividendos, recompra ou emissão de ações

### Métricas qualitativas

- **FCO positivo e crescente** é sinal de operação saudável
- **FCO menor que lucro líquido** consistentemente pode indicar manipulação contábil ou capital de giro deteriorando
- **FCO - capex de manutenção = fluxo de caixa livre** — o que sobra para acionistas e credores
- **Dividendos > FCO recorrentemente** — payout insustentável

### Sinal de alerta crítico

Receita crescendo + lucro contábil estável + FCO caindo = situação suspeita. Vale investigar capital de giro, política de crédito, eventos não recorrentes inflando o lucro contábil.

## Viabilidade de projeto

Quando o usuário pede análise de viabilidade (novo negócio, investimento em equipamento, expansão), aplique:

### Payback simples

Tempo até recuperar o investimento inicial em fluxos esperados. Métrica rápida, ignora o valor do dinheiro no tempo. Útil como filtro inicial — projetos com payback > 5-7 anos costumam ser rejeitados sem análise adicional.

### TIR (taxa interna de retorno)

Taxa que iguala o valor presente líquido dos fluxos a zero. Compare com o custo de capital da empresa. Se TIR < custo de capital, o projeto destrói valor.

### VPL (valor presente líquido)

Soma dos fluxos descontados pelo custo de capital, menos investimento inicial. VPL positivo = projeto cria valor.

### Limitações

- Estimativas de fluxo futuro são quase sempre otimistas — aplicar haircut
- A taxa de desconto escolhida pode mudar a conclusão; sempre testar com taxa mais conservadora
- Riscos não-financeiros (regulatório, operacional, dependência de um cliente) não entram nos números — devem aparecer como premissa qualitativa

## Exemplo resolvido 1 — empresa com lucro mas caixa fraco

**Situação:** Empresa cresceu receita 30% e lucro contábil 25% no ano, mas FCO caiu 10%.

- Diagnóstico provável: crescimento por crédito a cliente (contas a receber crescendo) ou estoque acumulado
- Verificações: ciclo de conversão de caixa (prazo médio de recebimento, prazo médio de estoque, prazo médio de pagamento)
- Sinalização: o crescimento de receita pode ser "comprado" com prazo maior, mascarando deterioração comercial
- Recomendação: investigar antes de comemorar o crescimento; pedir abertura de capital de giro

## Exemplo resolvido 2 — viabilidade de expansão

**Situação:** Usuário considera investir em equipamento que custa montante grande, com retorno estimado em fluxos crescentes por 7 anos.

- Cálculo: payback simples = 4 anos; TIR estimada = 18% a.a.; custo de capital da empresa = 14%
- Análise: VPL positivo, TIR acima do custo de capital. Decisão a princípio favorável.
- Sensibilidade: refazer com taxa de desconto 18% (margem de segurança) e ver se VPL ainda é positivo
- Premissa crítica: os fluxos crescentes assumem demanda estável; se demanda cair 20%, VPL pode virar negativo
- Sinalização: a recomendação depende da confiança na premissa de demanda; se há sinal de saturação do mercado, projeto é arriscado mesmo com TIR aparentemente atrativa

## Armadilhas comuns

- Olhar só o lucro contábil sem cruzar com caixa
- Aceitar TIR alta sem testar sensibilidade
- Confundir EBITDA com geração de caixa real
- Ignorar dívida ao avaliar uma empresa
- Comparar margens entre setores muito diferentes sem ajustar pela natureza do negócio
