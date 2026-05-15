# Framework — Análise de risco

Use sempre que a análise envolver alocação de patrimônio em ativos com risco (variável, renda fixa de crédito privado, alternativos). O risco entra como dimensão obrigatória na Seção 3 do output review-ready.

## Princípio fundamental

Existem dois tipos de "risco" e eles não devem ser confundidos:

- **Risco quantitativo (volatilidade)** — variabilidade dos retornos. Métrica útil para alocação e comparação, mas não captura tudo.
- **Risco qualitativo (perda permanente)** — probabilidade de não recuperar o capital investido. Este é o risco que realmente importa para o investidor.

Um ativo pode ter alta volatilidade e baixo risco de perda permanente (ex: índice de mercado diversificado em horizonte longo). E pode ter baixa volatilidade e alto risco de perda permanente (ex: empresa pequena com fraude latente que ainda não foi descoberta).

## Conceitos qualitativos essenciais

### Volatilidade

Tamanho das oscilações típicas. Em horizonte curto, volatilidade alta é problema (pode forçar venda em momento ruim). Em horizonte longo, volatilidade alta é menos problemática se o ativo subjacente é bom.

### Drawdown máximo

A maior queda de pico a vale que o ativo (ou carteira) já experimentou. Métrica útil para o investidor perguntar a si mesmo: "Eu aguentaria esse drawdown sem vender no fundo?"

Para investidor moderado, drawdowns acima de 30-40% costumam estourar a tolerância real (mesmo que declare ser arrojado).

### Correlação

Quão dois ativos se movem juntos. Diversificação real só existe quando os ativos têm correlação baixa em momentos de stress — e essa correlação tende a subir em crises ("when it rains, it pours").

Suposições típicas de correlação que falham em crise: ações de países diferentes (correlação sobe), renda fixa privada e ações (correlação sobe), criptoativos como hedge (geralmente correlacionam com risco em queda).

### Valor em risco (VaR) — conceito

Estimativa de quanto se pode perder em um horizonte definido com determinada probabilidade. Útil como heurística qualitativa, não como número preciso. Limitação clássica: o VaR diz quanto você perde nos casos "normais", não nos casos extremos.

## Riscos não-financeiros relevantes

A análise de risco precisa ir além de volatilidade e drawdown. Considere:

- **Risco regulatório** — mudanças de lei que afetam o produto (ex: nova tributação)
- **Risco de liquidez** — capacidade de sair da posição sem grande deságio
- **Risco de contraparte** — solidez do emissor ou da instituição custodiante
- **Risco de concentração** — patrimônio muito dependente de um único ativo, setor, país ou moeda
- **Risco cambial** — quando o ativo é em moeda diferente da que o usuário gasta
- **Risco operacional** — risco de erro humano, fraude, segurança (especialmente em ativos digitais)

Cada um desses entra como sub-item na Seção 3 ou 4 do output, conforme o peso na análise.

## Diretrizes de diversificação

### Intra-classe (dentro de uma classe de ativo)

- Em renda variável: pelo menos 10-15 papéis em setores diferentes para começar a diluir risco específico
- Em crédito privado: pulverizar entre emissores e setores; evitar concentração em um único emissor acima de pequena fração da alocação
- Em fundos imobiliários: diversificar tipos (logística, recebíveis, lajes, shopping)

### Entre classes

A diversificação que mais reduz risco é entre classes com **drivers diferentes**:

- Renda fixa de inflação x ações domésticas (drivers parcialmente opostos)
- Ações domésticas x ações no exterior (drivers regionais)
- Renda fixa x ouro (drivers opostos em alguns regimes)

## Limites práticos por perfil

Sugestões de teto de exposição por classe de risco, em % do patrimônio investido (não da renda):

### Conservador
- Renda variável total: ≤ 30%
- Ativo único arriscado: ≤ 5%
- Cripto: ≤ 2-5% (se houver)

### Moderado
- Renda variável total: 30-60%
- Ativo único arriscado: ≤ 10%
- Cripto: ≤ 5-10% (se houver)

### Arrojado
- Renda variável total: 60-80%
- Ativo único arriscado: ≤ 15-20%
- Cripto: ≤ 15% (se houver)

Esses são tetos sugeridos, não metas. Estouro de teto deve ser sinalizado na Seção 4 do output.

## Exemplo resolvido 1 — avaliar carteira concentrada

**Situação:** Usuário tem 80% do patrimônio investido em uma única ação de empresa que ele admira, perfil declarado moderado.

- **Risco quantitativo:** alta volatilidade e drawdown potencial > 50% mesmo em empresa boa
- **Risco qualitativo:** se houver fraude ou choque setorial, perda permanente significativa
- **Risco de concentração:** muito além do teto moderado (10%) para ativo único
- **Recomendação:** rebalancear gradualmente para reduzir a posição ao teto de perfil; vender de uma vez se a posição está muito acima do teto pode ser fiscalmente caro
- **Sinalização:** apontar que perfil declarado é incompatível com a alocação atual

## Exemplo resolvido 2 — comparar duas alocações

**Situação:** Usuário moderado considera (A) 50% renda fixa de inflação + 50% ações brasileiras vs (B) 50% renda fixa de inflação + 25% ações brasileiras + 25% ações exterior.

- **Risco quantitativo:** B tende a ter volatilidade similar mas menor drawdown em crises domésticas
- **Risco de concentração geográfica:** A está concentrada em ativos do mesmo país; B diversifica
- **Risco cambial:** B introduz exposição cambial que pode ser proteção em alguns cenários e custo em outros
- **Recomendação:** se o usuário consome em moeda local, B faz sentido como diversificação parcial — mas atenção ao timing de exposição cambial
- **Premissa explícita:** correlação entre mercados é menor em períodos normais que em crises agudas

## Armadilhas comuns

- Confundir volatilidade com risco — não são sinônimos
- Achar que diversificar dentro de uma única classe já reduz risco sistêmico
- Ignorar correlação em momentos de stress
- Não considerar o drawdown como teste psicológico real para o investidor
