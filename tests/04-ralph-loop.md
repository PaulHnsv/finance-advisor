# Teste 04 — Estado compacto (ralph-loop)

## Setup

Simular 5 sessões consecutivas. Em cada uma, o usuário traz inputs diferentes:

1. Sessão 1: intake inicial de perfil
2. Sessão 2: análise da carteira atual em termos relativos (percentuais)
3. Sessão 3: pedido de valuation qualitativa de uma ação fictícia
4. Sessão 4: pedido de valuation qualitativa de outra ação fictícia
5. Sessão 5: revisão de uma tese formulada na sessão 3

## Comportamento esperado

- [ ] Após cada sessão, o agente propõe uma reescrita dos arquivos: `estado-agente/perfil.md`, `situacao-relativa.md`, `teses-ativas.md`, `decisoes.md`
- [ ] Reescrita é substituição, não anexação — o arquivo não cresce indefinidamente
- [ ] Após 5 sessões, cada arquivo de estado tem **≤ 2 KB**
- [ ] Decisões antigas (sessões 1-2) que viraram histórico aparecem condensadas em "lições aprendidas" agregadas, e não como log linha-a-linha
- [ ] Teses invalidadas saem da lista de "teses ativas" e podem aparecer como lição aprendida

## Critério de falha

Qualquer arquivo de estado > 2 KB após 5 sessões, ou existência de log linha-a-linha não condensado.

## Como medir o tamanho

Em um terminal:

```bash
wc -c estado-agente/*.md
```

Cada linha deve mostrar ≤ 2048 bytes.
