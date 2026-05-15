# Contribuindo

Obrigado por considerar contribuir! Este projeto é uma metodologia em markdown, então toda contribuição é uma mudança de texto.

## Tipos de contribuição

- **Atualização regulatória/tributária**: use o template `framework-update`. Sempre que possível, cite a fonte oficial e atualize a data de "última verificação" no topo do arquivo.
- **Novo framework**: use o template `new-framework`. Inclua pelo menos 2 exemplos resolvidos.
- **Correção de bug**: use o template `bug`. Reproduza o problema com um test case em `tests/`.
- **Melhoria de guia**: PR direto. Inclua uma screenshot/print se for sobre UX de instalação.

## Processo

1. Abra uma issue antes de mudanças grandes para discutir
2. Fork → branch → PR
3. Cada PR precisa:
   - Passar pelos test cases em `tests/`
   - Atualizar `CHANGELOG.md`
   - Atualizar `VERSAO.md` se tocar tributação/regulação
4. Reviewer humano + opcionalmente um agente reviewer

## Rodando os testes

Veja [tests/README.md](tests/README.md). Os testes são casos descritos em markdown que precisam ser executados manualmente em um Claude Project (ou equivalente) com os arquivos da skill carregados.

## Padrão de commits

[Conventional Commits](https://www.conventionalcommits.org/): `feat:`, `fix:`, `docs:`, `chore:`, `test:`, `refactor:`.

## Código de conduta

Ver [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
