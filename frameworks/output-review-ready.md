# Framework — Saída revisável (review-ready)

Toda análise produzida pelo agente deve seguir o esquema abaixo. O objetivo é permitir que um consultor humano ou outro agente revise a análise sem precisar ler a conversa original.

## Esquema obrigatório

```markdown
# Análise: <título curto>

**Versão da skill:** v0.1 — corte do conhecimento: YYYY-MM
**Jurisdição assumida:** <BR / US / outro>
**Tipo de análise:** <pessoal / investimento / empresarial>

## 1. Resumo executivo

<= 5 linhas: o quê + recomendação principal + nível de confiança global.

## 2. Premissas utilizadas

- [P1] <premissa> — origem: <usuário disse / framework X / domínio público>
- [P2] ...

## 3. Análise estruturada

| Dimensão | Score (1-5) | Justificativa breve |
|---|---|---|
| Risco | ... | ... |
| Retorno esperado (qualitativo) | ... | ... |
| Liquidez | ... | ... |
| Alinhamento com perfil | ... | ... |
| Qualidade (se aplicável) | ... | ... |

## 4. Segunda opinião crítica

- Vieses possíveis detectados: ...
- Cenário pessimista plausível: ...
- O que precisaria ser verdade para a tese falhar: ...

## 5. Comparação com alternativas

| Opção | Quando faz sentido | Trade-off vs tese atual |
|---|---|---|

## 6. Recomendações concretas

1. <ação> — confiança: alta/média/baixa — premissa-chave: ...
2. ...

## 7. Para o revisor

- **Pontos que merecem verificação independente:** ...
- **Sinalizações de desatualização possível:** ...
- **Dados ausentes que mudariam a análise se conhecidos:** ...
- **Onde discordo de mim mesmo (alternativas igualmente defensáveis):** ...

## 8. Aviso

Esta análise é educacional e exploratória. Não é recomendação obrigatória nem definitiva. A decisão final é exclusivamente do usuário. Sempre seguir as leis vigentes na jurisdição aplicável. Para decisões com impacto significativo, consulte assessor financeiro registrado (CVM / SEC / equivalente) e contador habilitado.
```

## Regras de preenchimento

- **Seção 1 (Resumo)**: no máximo 5 linhas. Se a análise é complexa, a complexidade vai nas seções 3 e 4, não no resumo.
- **Seção 2 (Premissas)**: toda afirmação não-trivial vira uma premissa numerada com origem rastreável. Premissas extraídas do que o usuário disse devem citar isso explicitamente.
- **Seção 3 (Análise estruturada)**: score 1-5 onde 1 = muito desfavorável, 5 = muito favorável. Sempre incluir justificativa breve — não basta o número.
- **Seção 4 (Segunda opinião)**: o agente deve apresentar o argumento contrário mais forte que conseguir formular, mesmo que concorde com a tese.
- **Seção 5 (Comparação)**: pelo menos 2 alternativas relevantes. Se a única alternativa óbvia é "não fazer nada", também deve aparecer.
- **Seção 6 (Recomendações)**: ações concretas, não genéricas. Cada uma com confiança e premissa-chave para o revisor entender o raciocínio.
- **Seção 7 (Para o revisor)**: nenhum dos 4 sub-itens pode ser pulado. Se o agente acha que não há pontos a verificar, deve dizer isso explicitamente em vez de omitir.
- **Seção 8 (Aviso)**: texto literal, sem variação. Esta seção é obrigatória em toda saída.

## Exemplo resolvido

```markdown
# Análise: Alocar 100% da reserva de emergência em fundo DI de banco grande

**Versão da skill:** v0.1 — corte do conhecimento: 2026-05
**Jurisdição assumida:** BR
**Tipo de análise:** pessoal

## 1. Resumo executivo

Alocação razoável para a parcela de reserva, com ressalvas sobre taxa de administração e liquidez efetiva no resgate. Confiança global: alta sobre o conceito; média sobre o produto específico (depende da taxa).

## 2. Premissas utilizadas

- [P1] Reserva de emergência precisa de alta liquidez (D+0 idealmente) — origem: framework finanças-pessoais.
- [P2] Usuário declarou perfil moderado, horizonte > 10 anos para outros recursos — origem: estado-agente/perfil.md.
- [P3] Fundo DI tem rentabilidade próxima ao CDI menos taxa de administração — origem: domínio público.

## 3. Análise estruturada

| Dimensão | Score | Justificativa breve |
|---|---|---|
| Risco | 5 | Baixíssimo risco de crédito em fundo DI de grande banco |
| Retorno esperado | 3 | Atrelado ao CDI, sem prêmio |
| Liquidez | 4 | D+0 na maioria; verificar regra do fundo |
| Alinhamento com perfil | 5 | Reserva de emergência por definição é conservadora |

## 4. Segunda opinião crítica

- Viés possível: aversão à perda inflando o tamanho da reserva. Verificar se 6 meses de despesa é realista para o perfil.
- Cenário pessimista: taxa de administração acima de 0,5% a.a. corrói retorno; alternativa em Tesouro Selic via corretora reduz custo.
- Para a tese falhar: taxa do fundo DI > rendimento líquido do Tesouro Selic após IR. Verificar prospecto.

## 5. Comparação com alternativas

| Opção | Quando faz sentido | Trade-off |
|---|---|---|
| Tesouro Selic via corretora | Sempre que taxa < fundo DI | Liquidez D+1 vs D+0 |
| Poupança | Pequenos valores, simplicidade | Rentabilidade inferior em quase todos os cenários |
| CDB liquidez diária com FGC | Procura por garantia adicional | Atenção ao limite do FGC |

## 6. Recomendações concretas

1. Verificar taxa de administração do fundo DI antes de manter — confiança: alta — premissa-chave: P3.
2. Comparar com Tesouro Selic direto na mesma corretora — confiança: alta — premissa-chave: P3.
3. Confirmar tamanho da reserva (sugerido: 3-6 meses de despesa) — confiança: média — premissa-chave: P1.

## 7. Para o revisor

- Pontos a verificar: taxa de administração efetiva, regra de D+0 do fundo específico.
- Sinalizações de desatualização possível: nenhuma identificada (dados de 2026-05).
- Dados ausentes que mudariam a análise: valor exato da reserva e despesa mensal (em faixas).
- Onde discordo de mim mesmo: para valores pequenos a poupança pode ser razoável pela simplicidade.

## 8. Aviso

Esta análise é educacional e exploratória. Não é recomendação obrigatória nem definitiva. A decisão final é exclusivamente do usuário. Sempre seguir as leis vigentes na jurisdição aplicável. Para decisões com impacto significativo, consulte assessor financeiro registrado e contador habilitado.
```

## Armadilhas comuns

- Omitir a seção 7 quando o agente concorda com a tese. Errado: a seção 7 ainda precisa apontar o que um revisor deveria checar.
- Variar o texto do aviso da seção 8. Errado: ele é literal.
- Inflar o resumo executivo com detalhes. Errado: detalhes vão para seções 3-6.
- Misturar premissa com conclusão. Errado: premissas são insumos, conclusões aparecem em recomendações.
