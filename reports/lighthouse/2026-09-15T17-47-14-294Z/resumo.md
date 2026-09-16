# Resumo da auditoria Lighthouse

Executado em 2026-09-15T17:47:15.362Z, com 3 medições por página e perfil (mediana reportada).

Ambiente: Node v24.15.0, npm undefined, Lighthouse 13.4.1, Chrome undefined, win32 10.0.26200 x64.
Servidor: http://localhost:4173 (build otimizado `vite preview`, mocks MSW habilitados no cenário padrão).

| Página | Perfil | Performance | Accessibility | Best Practices | SEO | LCP (ms) | CLS | TBT (ms) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| inicio | mobile | 83 (meta 90, reprovado) | 89 (meta 95, reprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 3878 | 0.000 | 91 |
| inicio | desktop | 99 (meta 90, aprovado) | 90 (meta 95, reprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 904 | 0.000 | 5 |
| detalhe-do-nft | mobile | 86 (meta 90, reprovado) | 100 (meta 95, aprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 3609 | 0.000 | 39 |
| detalhe-do-nft | desktop | 90 (meta 90, aprovado) | 100 (meta 95, aprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 839 | 0.189 | 0 |

Resultados abaixo da meta devem ser justificados em ARCHITECTURE.md, com a causa identificada.