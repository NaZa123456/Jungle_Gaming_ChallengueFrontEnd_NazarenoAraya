# Resumo da auditoria Lighthouse

Executado em 2026-09-15T18:30:04.624Z, com 3 medições por página e perfil (mediana reportada).

Ambiente: Node v24.15.0, npm undefined, Lighthouse 13.4.1, Chrome undefined, win32 10.0.26200 x64.
Servidor: http://localhost:4173 (build otimizado `vite preview`, mocks MSW habilitados no cenário padrão).

| Página | Perfil | Performance | Accessibility | Best Practices | SEO | LCP (ms) | CLS | TBT (ms) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| inicio | mobile | 83 (meta 90, reprovado) | 100 (meta 95, aprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 3896 | 0.000 | 96 |
| inicio | desktop | 99 (meta 90, aprovado) | 100 (meta 95, aprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 906 | 0.000 | 5 |
| detalhe-do-nft | mobile | 86 (meta 90, reprovado) | 100 (meta 95, aprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 3602 | 0.000 | 32 |
| detalhe-do-nft | desktop | 90 (meta 90, aprovado) | 100 (meta 95, aprovado) | 96 (meta 95, aprovado) | 92 (meta 90, aprovado) | 842 | 0.189 | 0 |

Resultados abaixo da meta devem ser justificados em ARCHITECTURE.md, com a causa identificada.