# Segurança — o que não enviar ao agente

Este agente foi projetado para **não armazenar** dados pessoais identificáveis. Mesmo assim, dependendo da plataforma onde ele estiver rodando (Claude, ChatGPT, Copilot, outras), as mensagens podem ser registradas pela plataforma para fins de operação, suporte ou treinamento. Por isso:

## Não envie

- Número de documento de identificação (de qualquer país)
- Número de conta bancária, agência, IBAN
- Senhas, PINs, tokens, chaves de pagamento instantâneo
- Saldo bancário exato vinculado a uma instituição nomeada
- Endereço residencial
- Nomes completos (seus, de cônjuge, de dependentes)
- Códigos de autenticação enviados por SMS ou e-mail

## Sempre prefira

- **Faixas e categorias** em vez de valores exatos
  - "tenho cerca de 6 meses de reserva" em vez de um valor preciso em moeda
- **Percentuais** ao falar de alocação
  - "50% em renda fixa, 30% em ações, 20% em fundos imobiliários"
- **Rótulos** para o seu perfil
  - "perfil moderado, horizonte 10+ anos"
- **Tickers e nomes públicos** de empresas
  - "tenho posição em PETR4" sem mencionar quantidade exata

## Se você enviar dados sensíveis por engano

O agente é instruído a recusar a continuação da análise e pedir o reenvio sanitizado. Ele não vai persistir esses dados no estado escopado.

**Mas:** a plataforma onde você está pode reter o log da mensagem original. Em caso de erro:

1. Apague a mensagem (e a conversa, se possível) na plataforma
2. Verifique a política de retenção de dados da plataforma
3. Se for crítico, considere mudar de plataforma para uma com retenção mínima ou rodar localmente

## Para usuários sensíveis a privacidade

- Use plataformas que permitam desabilitar uso dos dados para treino
- Use contas dedicadas que não estejam ligadas ao seu e-mail pessoal principal
- Considere rodar o agente localmente quando possível

## Lembrete sobre o agente

O agente não pode garantir privacidade absoluta — ele opera dentro da plataforma que você escolher. A responsabilidade pela escolha da plataforma e pela higiene das mensagens é do usuário.
