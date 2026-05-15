# Framework — Perfil do investidor (intake escopado)

Este framework define o questionário de intake do agente quando o `estado-agente/perfil.md` está vazio ou desatualizado. **Princípio central:** coletar apenas categorias, faixas e rótulos — nunca valores absolutos identificáveis nem dados de identificação pessoal.

## Protocolo de intake

Faça **uma pergunta por turno**. Aguarde a resposta antes da próxima. Ao final, proponha o conteúdo completo do `estado-agente/perfil.md` para o usuário aprovar.

### Sequência de perguntas

1. **Jurisdição fiscal**
   "Em qual país você é tributado? Isso muda completamente quais produtos e regras eu vou usar."
   Opções típicas: Brasil, Estados Unidos, outro.

2. **Faixa etária**
   "Em qual faixa etária você está? (20-29, 30-39, 40-49, 50-59, 60+)"
   Razão: idade afeta horizonte e tolerância a risco; faixa é suficiente.

3. **Dependentes**
   "Você tem dependentes financeiros? (0, 1, 2, 3 ou mais)"
   Razão: dependentes afetam tamanho de reserva e seguros essenciais.

4. **Estabilidade da renda**
   "Como você classifica a estabilidade da sua renda principal? (alta, média, baixa)"
   Razão: estabilidade afeta tamanho da reserva de emergência recomendada.

5. **Tolerância a risco declarada**
   "Como você classifica sua tolerância a risco? (conservador, moderado, arrojado)"
   Pergunta de calibração na sequência: "Se sua carteira caísse 30% em um mês, sua reação seria: vender tudo, ficar desconfortável mas manter, ou aproveitar para comprar mais?"

6. **Horizonte principal**
   "Qual o horizonte do dinheiro que você está pensando em investir? (até 2 anos, 2-5 anos, 5-10 anos, 10+ anos)"
   Razão: horizonte é o filtro mais importante para alocação.

7. **Objetivos (rotulados)**
   "Quais são seus objetivos financeiros principais? (rótulos: reserva, aposentadoria, imóvel, educação dos filhos, viagens, abrir negócio, outros)"
   Pode listar mais de um.

8. **Restrições e preferências**
   "Há algo que você prefere não investir por princípio? (ex: tabaco, armas, combustíveis fósseis)"

## Regras de inferência de perfil

Quando a tolerância declarada diverge da resposta de calibração (pergunta 5), o agente deve apontar essa divergência ao usuário e sugerir a classificação mais conservadora das duas, deixando a escolha final com o usuário.

Mapeamento aproximado de tolerância → alocação de referência (a ser refinado por `frameworks/investimentos.md`):

- Conservador: 70-90% renda fixa, 10-30% variável
- Moderado: 40-70% renda fixa, 30-60% variável
- Arrojado: 20-40% renda fixa, 60-80% variável

Esses são apenas pontos de partida — o framework de investimentos refina conforme objetivos e horizonte.

## Estrutura do `estado-agente/perfil.md` que sai pronto

```markdown
# Perfil (escopado)

- Faixa etária: <faixa>
- Jurisdição fiscal: <país>
- Dependentes: <0 | 1 | 2 | 3+>
- Estabilidade da renda: <alta | média | baixa>
- Tolerância a risco declarada: <conservador | moderado | arrojado>
- Horizonte principal: <≤2 anos | 2-5 anos | 5-10 anos | 10+ anos>
- Objetivos: <lista de rótulos>
- Restrições: <lista; ou "nenhuma declarada">

---
Última revisão: YYYY-MM-DD
```

## Lidando com inconsistências

Se em sessões futuras o usuário disser algo que contradiz o perfil registrado (ex: declara perfil moderado mas pede análise de operações de altíssima alavancagem), o agente deve apontar a contradição explicitamente antes de prosseguir, e perguntar se o perfil deve ser atualizado.

## O que nunca pedir no intake

- CPF, CNPJ, número de documento de identidade de qualquer tipo
- Nome completo do usuário ou de terceiros
- Número de conta bancária, agência, IBAN
- Senhas, PINs, chaves de pagamento
- Endereço residencial
- Salário exato (sempre use faixa relativa ou ratio com despesa)
- Saldo exato em conta vinculado a uma instituição

## Exemplos de perfis preenchidos (ficcionais)

### Exemplo 1: jovem, conservador, sem dependentes

```markdown
# Perfil (escopado)

- Faixa etária: 20-29
- Jurisdição fiscal: BR
- Dependentes: 0
- Estabilidade da renda: alta
- Tolerância a risco declarada: conservador
- Horizonte principal: 10+ anos
- Objetivos: reserva, aposentadoria
- Restrições: nenhuma declarada
```

### Exemplo 2: meia-idade, moderado, com filhos

```markdown
# Perfil (escopado)

- Faixa etária: 40-49
- Jurisdição fiscal: BR
- Dependentes: 2
- Estabilidade da renda: média
- Tolerância a risco declarada: moderado
- Horizonte principal: 5-10 anos
- Objetivos: aposentadoria, educação dos filhos, imóvel
- Restrições: nenhuma declarada
```

### Exemplo 3: arrojado com dilema de coerência

Cenário: usuário declara perfil arrojado, mas na pergunta de calibração diz que venderia tudo após uma queda de 30%.

Ação do agente: apontar a divergência, sugerir reclassificar como moderado, e deixar o usuário decidir. Se o usuário insiste em "arrojado", registrar isso no perfil mas anotar a divergência em uma seção "observações" do `perfil.md`.

## Armadilhas comuns

- Pedir várias perguntas de uma vez — errado: faça uma de cada vez.
- Anotar idade exata em vez da faixa — errado: sempre faixa.
- Aceitar "tolerância arrojada" sem testar com cenário de queda — errado: sempre calibrar.
- Pular o intake porque o usuário parece experiente — errado: o estado precisa estar registrado para sessões futuras.
