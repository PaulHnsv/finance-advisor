# Changelog

Todos os registros seguem [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) e este projeto adota [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [0.3.0] — 2026-05-15

### Added
- Framework `frameworks/ralph-loop.md` — ralph-loop autônomo silencioso para Claude Code/Cowork
- Framework `frameworks/compressao-estado.md` — regras determinísticas D4.1-D4.4 (invalidação, TTL, pressão, limite 2 KB)
- Bloco `## Lições aprendidas (arquivado)` em todos os templates de `estado-agente/`
- Comandos de revert: `reverta` e `reverta <arquivo>` (modo autônomo)
- Test cases 10 (ralph-loop autônomo) e 11 (compressão determinística)

### Changed
- `AGENT.md`: protocolo de persistência referencia os dois novos frameworks; seção antiga de fim de sessão substituída
- `guias-instalacao/claude.md`: documenta modo autônomo Cowork/Code com `.bak` e revert
- `guias-instalacao/chatgpt.md` + `copilot.md`: explicitam modo manual
- Templates `teses-ativas` e `decisoes`: seção "Lições aprendidas" renomeada para padronizar com novos templates `perfil` e `situacao-relativa`

### Behavior change (modo autônomo)
- No Claude Code/Cowork, mudanças de estado são escritas silenciosamente durante a sessão
- Resumo único de mudanças no encerramento da sessão
- Backup automático (`.bak`) por arquivo, sobrescrito a cada nova escrita

## [0.2.0] — 2026-05-15

### Added
- Framework `frameworks/visualizacao.md` com catálogo V1 (radar), V2 (pizza), V3 (quadrante)
- Gatilho automático de visualização em análises de investimento (`frameworks/investimentos.md`)
- Opt-in explícito de visualização em análises pessoais e empresariais
- Capability matrix de renderização por plataforma (Claude / ChatGPT / Copilot)
- Test cases 07, 08, 09 cobrindo happy path, opt-in e degradação
- Protocolo de visualização no `AGENT.md`

### Changed
- `frameworks/output-review-ready.md` referencia regras de localização dos visuais
- Guias de instalação documentam capacidades de visualização por plataforma

## [0.1.0] — 2026-05-14

### Added
- Definição inicial do agente (`AGENT.md`)
- Frameworks de perfil, risco, finanças pessoais, investimentos, valuation e finanças empresariais
- Framework de segunda opinião crítica
- Saída padronizada revisável por terceiros
- Tributação BR e US separadas, com data de última verificação
- Guias de instalação para Claude, ChatGPT e Copilot
- Guia de segurança para o usuário (orientação sobre dados sensíveis)
- Conjunto de test cases manuais
