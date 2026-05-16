# Testes

Estes testes são cenários executáveis manualmente em um assistente de IA (Claude Project preferencial). Não há runner automatizado na v0.1.

## Como rodar

1. Abra um Claude Project novo (ou um Custom GPT de teste)
2. Carregue os arquivos indicados em "Setup" do teste
3. Cole a "Entrada do usuário" exatamente como descrita
4. Compare a resposta do agente com a checklist de "Comportamento esperado"
5. Se todos os itens marcam ✅, o teste passa

## Lista de testes

- `01-intake-perfil.md` — intake inicial de novo usuário (sem coleta de dados sensíveis)
- `02-pii-detection.md` — recusa de entrada com dados sensíveis
- `03-tese-fraca.md` — crítica de tese propositalmente fraca
- `04-ralph-loop.md` — estado compacto após múltiplas sessões
- `05-review-ready.md` — formato de saída revisável por terceiros
- `06-sinalizacao-desatualizacao.md` — sinalização para informação posterior à data de corte
- `07-visualizacao-investimento.md` — visualização padrão em análise de investimento (Claude)
- `08-visualizacao-opt-in.md` — opt-in em análises pessoal/empresarial
- `09-visualizacao-degradacao.md` — degradação em plataformas sem Artifacts/Code Interpreter

## Critério geral de release

A v0.2.0 só pode ser tagueada quando os 9 testes passarem.
