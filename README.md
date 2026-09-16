# Kurio — Marketplace de NFTs

Frontend do desafio técnico de Marketplace de NFTs. Uma loja de colecionáveis digitais em que o usuário
navega pelo catálogo, filtra e busca NFTs, favorita itens, monta um carrinho, aplica cupons e conclui uma
compra com carteira de blockchain simulada — tudo com dados e integrações simuladas (não há backend real
nem blockchain envolvidos).

Tecnologias principais: **React + TypeScript**, **TanStack Router**, **TanStack Query**, **Axios**,
**Socket.IO**, **Tailwind CSS**, **shadcn/ui** (Radix), **MSW** e **Playwright**.

A aplicação está publicada em https://marketplace-de-nft-s.vercel.app e o código-fonte em
https://github.com/NaZa123456/Jungle_Gaming_ChallengueFrontEnd_NazarenoAraya.

## Sumário

- [Stack tecnológico](#stack-tecnológico)
- [Requisitos](#requisitos)
- [Instalação](#instalação)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Execução](#execução)
- [Funcionalidades](#funcionalidades)
- [Mocks e cenários](#mocks-e-cenários)
- [Testes e verificações](#testes-e-verificações)
- [Regressão visual](#regressão-visual)
- [Acessibilidade](#acessibilidade)
- [Lighthouse](#lighthouse)
- [Deploy](#deploy)
- [Validação final](#validação-final)
- [Pull Request / Entrega](#pull-request--entrega)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Documentação adicional](#documentação-adicional)

## Stack tecnológico

| Camada | Tecnologia | Papel no projeto |
| --- | --- | --- |
| Linguagem | **TypeScript** (`v6`, compilado com `tsc -b`) | Tipagem de contratos entre transporte, estado e UI |
| UI | **React 19** | Biblioteca de componentes |
| Build e dev server | **Vite 8** + `@vitejs/plugin-react` | Dev server, build otimizado e preview |
| Routing | **TanStack Router 1.x** | Rotas por arquivo, parâmetros de busca (filtros persistidos na URL) e guardas de sessão |
| Estado assíncrono | **TanStack Query 5.x** | Consultas, mutations, cache, retries e invalidação após eventos |
| Cliente HTTP | **Axios** | Única via de acesso REST (nenhum `fetch` direto no app) |
| Estilos | **Tailwind CSS 4** + `@tailwindcss/vite` | Design system em classes utilitárias |
| Componentes de UI | **shadcn/ui** (Radix UI) | Dialog, Dropdown, Select, Tabs, Slider, Toast, Avatar etc. (`src/components/ui`, convenção documentada em `components.json`) |
| Ícones | **lucide-react** | Iconografia |
| Validação | **zod** | Validação de formulários |
| Mocking/API | **MSW 2** + **@mswjs/socket.io-binding** | Mocks REST e eventos Socket.IO na camada de rede |
| Tempo real | **socket.io-client** | Eventos `nft.updated` e `order.updated` |
| Testing | **Playwright** (`@playwright/test`) | Testes E2E, teclado/acessibilidade e regressão visual |
| Performance | **lighthouse** + **chrome-launcher** | Auditoria mobile/desktop |
| Utilitários | **clsx**, **class-variance-authority**, **tailwind-merge** | Composição de classes (padrão shadcn/ui) |
| Fontes | **@fontsource/archivo**, **@fontsource/inter** | Carregamento local de fontes |
| CLI auxiliar | **tsx**, **cross-env**, **@tanstack/router-plugin** | Scripts de geração e execução |

As dependências são instaladas com `npm install` (ver [Instalação](#instalação)); o arquivo
`package.json` é a fonte da verdade para a lista completa de pacotes.

## Requisitos

- **Node.js** 20.19 ou superior (ou 22.12+); o projeto foi desenvolvido e validado com Node 24.
- **npm** 10 ou superior (testado com npm 11).
- Sistema operacional: a aplicação roda em Windows, Linux ou macOS. Na validação desta entrega foi
  usado Windows 10/11.

Para conferir a instalação:

```bash
node -v
npm -v
```

## Instalação

Passo a passo do zero:

```bash
# 1. Clone o repositório
git clone https://github.com/NaZa123456/Jungle_Gaming_ChallengueFrontEnd_NazarenoAraya.git

# 2. Entre na pasta do projeto (nome = Jungle_Gaming_ChallengueFrontEnd_NazarenoAraya, o mesmo do repositório)
cd Jungle_Gaming_ChallengueFrontEnd_NazarenoAraya

# 3. Instale as dependências (usa package-lock.json, já presente no repositório)
npm install
```

Não é necessário criar nenhum arquivo `.env` para rodar localmente (ver
[Variáveis de ambiente](#variáveis-de-ambiente)). Os mocks (MSW) já estão habilitados por padrão.

**Navegadores para os testes E2E** (Playwright): instale o Chromium e o WebKit, pois o projeto tem
três projetos de teste — desktop e mobile rodam em Chromium e o tablet usa WebKit (device iPad):

```bash
npx playwright install chromium webkit
```

Se o `public/mockServiceWorker.js` não existir ou precisar ser regenerado (por exemplo, após atualizar
o MSW), o worker pode ser recriado com:

```bash
npm run msw:init
```

## Variáveis de ambiente

| Variável | Padrão | Descrição |
| --- | --- | --- |
| `VITE_ENABLE_MOCKS` | `true` | Liga/desliga o MSW (REST + Socket.IO simulado). Use `false` apenas se for apontar para um backend real, o que está fora do escopo deste desafio. |
| `VITE_API_BASE_URL` | `/api` | Base usada pelo cliente Axios (`src/lib/axios.ts`). |
| `VITE_SOCKET_URL` | mesma origem (`ws://`/`wss://`) | Endpoint usado pelo cliente `socket.io-client`. Em produção, deixe vazio para usar a origem publicada automaticamente. |

Para sobrescrever, crie um `.env.local` (ignorado pelo Git) ou exporte a variável na linha de comando:

```bash
VITE_ENABLE_MOCKS=false VITE_API_BASE_URL=https://minha-api.exemplo.com npm run dev
```

## Execução

### Desenvolvimento

```bash
npm run dev
```

Abre em http://localhost:5173 com os mocks ativos por padrão. A variante explícita é
`npm run dev:mocks`. O servidor usado pelos testes E2E é `npm run dev:e2e` (porta 5173 fixa).

### Build de produção

```bash
npm run build
```

Executa a verificação de tipos (`tsc -b`) e gera o build otimizado em `dist/`.

### Preview (produção local)

```bash
npm run preview
```

Serve o build de produção em http://localhost:4173 (porta usada também pela auditoria Lighthouse).

### Comandos disponíveis

Todos os comandos abaixo existem em `package.json` e foram verificados:

| Comando | O que faz |
| --- | --- |
| `npm run dev` | Sobe o servidor de desenvolvimento (Vite) com os mocks ativos por padrão. |
| `npm run dev:mocks` | Igual ao anterior, forçando `VITE_ENABLE_MOCKS=true` explicitamente. |
| `npm run dev:e2e` | Vite fixo em `127.0.0.1:5173` com mocks ativos (usado pelo Playwright). |
| `npm run build` | Verifica os tipos (`tsc -b`) e gera o build de produção (`vite build`). |
| `npm run preview` | Serve o build de produção localmente. |
| `npm run typecheck` | Roda apenas a verificação de tipos (`tsc -b --noEmit`). |
| `npm run lint` | Roda o ESLint em todo o projeto. |
| `npm run test:e2e` | Executa a suíte Playwright (desktop, tablet e mobile). |
| `npm run test:e2e:update-snapshots` | Regenera/atualiza as baselines de regressão visual. |
| `npm run test:e2e:ui` | Abre o modo interativo do Playwright. |
| `npm run test:e2e:report` | Abre o último relatório HTML gerado pelo Playwright. |
| `npm run release:verify` | Após `npm ci`, regenera baselines visuais, roda o Lighthouse e executa o `predeploy:check`. |
| `npm run lighthouse` | Builda, sobe o preview e audita Início e Detalhe do NFT (mobile e desktop). |
| `npm run predeploy:check` | Bloqueia a entrega se faltarem snapshots visuais, relatórios Lighthouse, documentação ou scripts obrigatórios. |
| `npm run generate:nft-avatars` | Regenera os avatares determinísticos dos NFTs em `public/images/nfts`. |
| `npm run msw:init` | Regenera o service worker do MSW em `public/`. |

## Funcionalidades

### Home

- Hero de apresentação, catálogo de NFTs com busca por texto, filtros combináveis por **categoria** e
  **rede**, ordenação e paginação — tudo persistido na URL (funciona refresh e historical).
- Contador de itens no carrinho, seção de destaque, banners de criadores e posts educativos.
- Estados de carregamento com **skeletons** (com shimmer), estado vazio e estado de erro com "Tentar
  novamente" (retry).
- Favoritar e adicionar ao carrinho diretamente do catálogo, com atualização otimista e rollback.

### Detalhe do NFT

- Acesso direto por URL (`/nfts/:nftId`), com tratamento de NFT inexistente (página de não encontrado).
- Informações do item, edição disponível, preço em ETH (string decimal), quantidade e ações de
  favoritar/adicionar ao carrinho condicionadas à disponibilidade.
- Quantidade com controles acessíveis (ver [Acessibilidade](#acessibilidade)).

### Carrinho

- Adicionar, alterar quantidade e remover itens, respeitando disponibilidade por NFT e edição.
- Persistência após refresh; itens de visitante são preservados ao autenticar (merge guest → usuário).
- Aplicar e remover **cupom**, com tratamento de código inválido ou expirado.
- Cotação com subtotal, desconto, taxa de rede e total coerentes com a resposta da API.
- Reflete alterações de preço/disponibilidade recebidas via evento em tempo real enquanto está aberto.

### Checkout / Pagamento

- Seleção das carteiras cadastradas, com escolha de **rede** e **provedor** coerentes e simulação de
  conexão, recusa e desconexão via API.
- Revalidação de preço, disponibilidade, cupom e taxas antes de confirmar; mudanças exigem nova
  confirmação (cotação marcada como `stale`).
- Criação idempotente do pedido (`Idempotency-Key`): cliques repetidos ou reenvios após timeout não
  geram compra duplicada.

### Confirmação de compra

- Pedido em estados `pending`, `confirmed` e `declined`, com recuperação após refresh ou reconexão.
- Recibo com o **snapshot** dos valores no momento da compra (não muda após alterações no catálogo).
- Confirmação exibida somente para pedido efetivamente confirmado na simulação.

### Conta, perfil e carteiras

- Cadastro, login, logout e sessão integrados à API simulada; expiração tratada de forma centralizada,
  preservando o contexto para retomada.
- Checkout, perfil, carteiras, favoritos e pedidos exigem autenticação. Logout e troca de usuário
  limpam caches e subscriptions da sessão anterior (isolamento de dados entre usuários).
- Edição de perfil (nome, e-mail, avatar) e senha, e gestão de carteiras, com validação e erros da API.

### Tempo real (Socket.IO)

- Evento `nft.updated`: atualiza preço/disponibilidade no catálogo, detalhe e carrinho.
- Evento `order.updated`: atualiza o estado do pedido na confirmação.
- Envelope com identidade, recurso e versão; o cliente descarta eventos duplicados/antigos e
  reconcilia via REST ao reconectar.

### Responsividade

- Desktop, tablet (768px) e mobile (até 320px), conforme os frames do Figma (Desktop/Mobile) e faixa
  de 768px adaptada do layout mobile.
- Menu principal mobile, barra de navegação inferior fixa (Início, Favoritos, Explorar, Carrinho,
  Perfil) e filtros em drawer no mobile.
- Imagens e fontes locais — execução local sem dependência de rede.

## Mocks e cenários

Os mocks rodam na camada de rede (MSW) e mantêm estado consistente entre catálogo, favoritos, carrinho,
perfil, carteiras e pedidos, persistido em `localStorage` (`kurio-mock-db-v2`) para sobreviver a refresh.
O reset restaura integralmente fixtures, sessões, carrinhos, favoritos, carteiras e pedidos.

> Os componentes, hooks e o cliente Axios não contêm respostas fictícias: toda a simulação vive na
> camada de rede (ver `docs/mocking.md`).

### Contas de demonstração

Nenhuma senha real é usada — são credenciais fictícias, válidas apenas na base simulada:

| E-mail | Senha | Observação |
| --- | --- | --- |
| `andreza.colecionadora@kurio.test` | `kurio123` | Possui carteira principal cadastrada. |
| `daniel.dev@kurio.test` | `kurio123` | Não possui carteira cadastrada (útil para testar o cadastro de carteira). |

### Cupons

| Código | Efeito |
| --- | --- |
| `KURIO10` | 10% de desconto. |
| `BEMVINDO` | 0,05 ETH de desconto fixo. |
| `EXPIROU5` | Expirado (usado para testar o erro de cupom inválido). |

### Cenários dos mocks

Ative pela query string, por exemplo `http://localhost:5173/?scenario=slow`:

| Cenário | Efeito |
| --- | --- |
| `slow` | Latência de 1,5 a 3,5 segundos em todas as respostas. |
| `flaky` | Latência variável combinada com ~30% de falhas transitórias (5xx). |
| `offline` | Recusa as conexões do Socket.IO, simulando queda de tempo real. |
| `order-timeout` | O pedido criado permanece em `pending`, sem resolver — usado para testar a recuperação por idempotência após timeout. |
| `payment-declined` | O próximo pedido criado é recusado pela simulação. |

Existe ainda o cenário interno `wallet-declined` (recusa da conexão da carteira no checkout).

**Reset do cenário:** enquanto os mocks estiverem ativos, um botão fixo **"Resetar cenário"** aparece no
canto inferior direito. Ele chama `POST /api/dev/reset` e recarrega a página, restaurando o estado inicial.

### Como reproduzir os fluxos de falha

Todos os passos assumem a aplicação rodando com mocks ativos (`npm run dev`).

1. **Sessão expirada / acesso não autorizado** — faça login, acesse `Perfil` ou `Carrinho`, resete o
   cenário em outra aba e tente concluir uma ação protegida: a UI trata a sessão inválida e redireciona
   ao login preservando o contexto de retomada.
2. **Cupom inválido ou expirado** — no carrinho, aplique `EXPIROU5` ou um código inexistente.
3. **Preço/disponibilidade alterada durante a compra** — adicione um NFT ao carrinho, altere o preço ou
   a disponibilidade (via `POST /api/dev/simulate/nft-update` ou a ação na UI de desenvolvimento) e
   finalize o checkout: a cotação desatualizada bloqueia a confirmação até nova revisão.
4. **Timeout após criação do pedido** — acesse com `?scenario=order-timeout`, finalize a compra e dê
   refresh com o pedido pendente: o mesmo pedido é recuperado via `orderId`, sem nova compra.
5. **Pagamento recusado** — acesse com `?scenario=payment-declined` e finalize a compra: o pedido é
   apresentado como recusado, não como confirmado.
6. **Falha de conexão / respostas 4xx e 5xx / latência fora de ordem** — acesse com `?scenario=flaky`
   e navegue pelo catálogo: os estados de carregamento, erro e nova tentativa devem se comportar
   corretamente.
7. **Queda de tempo real** — acesse com `?scenario=offline` com um pedido pendente, depois remova o
   parâmetro e recarregue: a reconexão reconcilia o estado do pedido via REST.
8. **Conflito de cadastro / validação de formulário** — cadastre um e-mail já usado ou envie formulários
   com campos inválidos para ver as mensagens da API simulada.

## Testes e verificações

Os testes E2E ficam em `tests/e2e` e rodam contra o dev server com mocks (`npm run dev:e2e`), em três
projetos: `chromium-desktop` (1440×900), `chromium-tablet` (768×1024, WebKit/iPad) e `chromium-mobile`
(393×851, Pixel 5).

```bash
npm run test:e2e
```

- Relatório HTML e traces das falhas em `playwright-report/` (abrir com `npm run test:e2e:report`).
- Cada teste parte de um estado isolado (reset do cenário via endpoint de dev).
- Controle de relógio, latência e disparo de eventos nos testes sensíveis a tempo.

### Suítes de teste

| Suíte | Cobre |
| --- | --- |
| `catalogSearchAndFilters.spec.ts` | Busca, filtros combinados, ordenação, paginação e restauração pelo histórico. |
| `nftDetail.spec.ts` | Acesso direto ao detalhe e tratamento de NFT inexistente. |
| `authenticationSession.spec.ts` | Cadastro, login, expiração de sessão, logout e troca de usuário (inclui merge guest → user). |
| `favorites.spec.ts` | Favoritos, falha de mutation e recuperação do estado. |
| `cart.spec.ts` | Quantidades, remoção, cupom e persistência após refresh/login. |
| `checkoutPurchase.spec.ts` | Compra completa (catálogo → recibo), falha de pagamento, clique repetido e timeout com idempotência. |
| `profileAndWallets.spec.ts` | Edição de perfil, avatar, senha e carteiras, com validação. |
| `realtimeUpdates.spec.ts` | Alterações de preço/disponibilidade via Socket.IO, eventos duplicados/antigos e retomada de pedido pendente. |
| `accessibilityAndKeyboard.spec.ts` | Navegação por teclado, foco de diálogos, labels e validação de formulários. |
| `visualRegression.spec.ts` | Regressão visual de início, detalhe, carrinho e pagamento (ver próxima seção). |

### Verificações de qualidade executadas

- **`npm run typecheck`** — passa sem erros.
- **`npm run lint`** — executado; os pontos reportados pelo ESLint que restam são pré-existentes e
  estão fora do escopo de alteração desta entrega (regra `react-hooks/set-state-in-effect` em
  `src/routes/index.tsx` e `src/routes/checkout.tsx`, além de avisos de dependência de efeitos). Nenhum
  deles foi introduzido por esta entrega.
- **E2E Chromium (desktop e mobile)** — suítes de regressão visual (carrinho/pagamento) e de
  acessibilidade foram executadas durante a validação; os resultados com limitações conhecidas estão
  detalhados na [Validação final](#validação-final).
- **Deploy** — a URL pública respondeu HTTP 200 durante a validação (16/09/2026).

## Regressão visual

Há **8 baselines versionados** em `tests/e2e/visualRegression.spec.ts-snapshots/`, cobrindo:

- `home` (início), `nft-detail`, `cart` (carrinho) e `payment` (pagamento);
- cada uma em **desktop** e **mobile** (`*-chromium-desktop.png` / `*-chromium-mobile.png`).

Os baselines foram gerados por **execuções reais do navegador** (datados de 15 e 16/09/2026) e os
snapshots não incorporam a plataforma do host (são portáveis entre Windows/Linux/macOS).

- **Carrinho e pagamento** — as baselines foram validadas na execução mais recente (desktop e mobile)
  e **passam** na regressão visual.
- **Início e detalhe** — as baselines existem e estão versionadas; porém ficaram **dessincronizadas com
  o render atual** após os últimos ajustes de UI (diferença observada: início desktop ~0.11, detalhe
  desktop ~0.49, início mobile ~0.41 e detalhe mobile ~0.65 de relação de pixels). Antes da avaliação,
  recomenda-se regenerá-las, quando o layout alvo for considerado final:

```bash
npm run test:e2e:update-snapshots
```

Esse comando regenera **todas** as baselines da suíte visual.

## Acessibilidade

O projeto foi construído com navegação por teclado, foco visível e semântica apropriada (labels, landmarks
e mensagens de erro), usando a gestão de foco do Radix para diálogos e drawers. Entre as correções e
ajustes específicos desta entrega:

- **Menu mobile**: o botão que abre/fecha o menu expõe `aria-label`, `aria-expanded` e
  `aria-controls`, o painel é um `<nav aria-label="Menu principal">` e agora há também um **botão de
  fechar** explícito e acessível em teclado dentro do menu, além do toggle do cabeçalho.
- **Seletor de quantidade**: no detalhe do NFT, os controles de −/＋/valor estão agrupados com
  `role="group"` e `aria-label="Quantidade"`; os botões possuem rótulos acessíveis. Os controles de
  quantidade do carrinho em mobile também possuem rótulos acessíveis.
- **Barra de navegação inferior mobile**: itens centralizados e alinhados de forma simétrica nas
  resoluções mobile testadas (393, 430, 360 e 320 px de largura).
- **Skip link** "Pular para o conteúdo", foco visível ao navegar por teclado e mensagens de erro
  apresentadas de forma não dependente apenas de cor.

### Verificação

- A suíte `accessibilityAndKeyboard.spec.ts` cobre navegação por teclado, foco de diálogos e validação
  de formulários; os testes diretamente relacionados às correções (rótulo do seletor de quantidade e
  botão de fechar do menu) **passam**.
- **Limitação conhecida**: a suíte ainda reporta falhas pré-existentes de asserção (8 testes), mapeadas
  durante a auditoria — referem-se a seletores ambíguos/estado em casos como o menu em foco e a tabela
  do carrinho em mobile, e **não estão relacionadas** às correções de acessibilidade entregues.

## Lighthouse

A auditoria é executada com `npm run lighthouse` — builda a aplicação, sobe o preview otimizado
(porta 4173) e roda **3 medições** por página (Início e Detalhe do NFT) e perfil (mobile/desktop), com o
cenário padrão dos mocks, reportando a **mediana**. Relatórios HTML/JSON e resumo ficam em
`reports/lighthouse/<data>/` (execução de referência: `2026-09-15T19-06-28-083Z`, Lighthouse 13.4.1,
Node v24.15.0).

Metas do desafio: Performance ≥ 90, Accessibility ≥ 95, Best Practices ≥ 95, SEO ≥ 90.

| Página | Perfil | Performance | Accessibility | Best Practices | SEO | LCP (ms) | CLS | TBT (ms) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Início | mobile | 83 <sub>(meta 90)</sub> | 100 | 96 | 92 | 3889 | 0.000 | 83 |
| Início | desktop | 99 | 100 | 96 | 92 | 897 | 0.000 | 4 |
| Detalhe do NFT | mobile | 86 <sub>(meta 90)</sub> | 100 | 96 | 92 | 3602 | 0.000 | 32 |
| Detalhe do NFT | desktop | 90 | 100 | 96 | 92 | 863 | 0.189 | 0 |

**Resultado conhecido:** a **Performance mobile** ficou abaixo da meta de 90 em ambas as páginas
(83 e 86). A auditoria emite essa pontuação com emulação de rede lenta (Slow 4G) e CPU 4×, com LCP
elevado no perfil mobile (~3,9 s e ~3,6 s) — fatores já identificados. As demais categorias atingem as
metas em todas as combinações (Accessibility 100, Best Practices 96, SEO 92; Performance desktop 99/90).
Não houve nenhuma alteração exclusiva para "melhorar" a pontuação da auditoria (o requisito do desafio
proíbe esse tipo de simplificação).

**Limitações de registro:** no `resumo.md` gerado, os campos de ambiente `npm` e `Chrome` aparecem como
`undefined` (o `resumo.json` e os relatórios HTML/JSON contêm os dados completos de cada execução).

Independentemente das métricas sintéticas, a aplicação foi validada **manualmente na versão publicada**:
os fluxos de navegação, home, detalhe, carrinho, checkout, pagamento e confirmação, o menu mobile e o
comportamento responsivo foram percorridos e funcionam corretamente, com experiência fluida.

## Deploy

A aplicação é publicada como site estático na **Vercel** (build via `npm run build`), com mocks
habilitados no ambiente publicado (`VITE_ENABLE_MOCKS=true` já é o padrão — não é necessário configurar
nenhuma variável para o deploy funcionar). Não há uso da CLI da Vercel: o deploy é feito pela
**integração Git** — todo push para a branch `main` gera automaticamente um novo deployment de produção.

Acesso direto e refresh de qualquer rota funcionam graças ao `vercel.json` na raiz, que redireciona
todas as rotas para `index.html` (fallback de SPA):

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

- **Repositório:** https://github.com/NaZa123456/Jungle_Gaming_ChallengueFrontEnd_NazarenoAraya
- **URL pública:** https://marketplace-de-nft-s.vercel.app

Use as [contas de demonstração](#contas-de-demonstração) para testar o fluxo completo na versão
publicada. A URL foi verificada durante a validação final (HTTP 200).

## Validação final

Resumo das verificações realizadas na etapa final de validação:

| Verificação | Resultado |
| --- | --- |
| `npm run typecheck` | ✅ Passa sem erros |
| `npm run lint` | ⚠️ Executado; restam erros/avisos pré-existentes do ESLint (ver nota abaixo) |
| Regressão visual — carrinho e pagamento | ✅ Passam (desktop e mobile) |
| Regressão visual — início e detalhe | ⚠️ Baselines versionadas, porém dessincronizadas com o render atual (regenerar com `npm run test:e2e:update-snapshots`) |
| Suíte a11y/teclado | ⚠️ Testes das correções passam; 8 falhas pré-existentes de asserção mapeadas |
| Lighthouse | ✅ Executado com relatórios versionados; Performance mobile abaixo da meta (ver seção Lighthouse) |
| Deploy público | ✅ URL responde HTTP 200 |
| Revisão manual da aplicação publicada | ✅ Flows, responsividade e interações funcionais (ver abaixo) |

**Revisão manual:** foi realizada uma revisão manual da aplicação publicada, percorrendo os principais
fluxos e o comportamento responsivo: navegação, home, detalhe do NFT, carrinho, checkout, pagamento,
confirmação, menu mobile, responsividade (desktop/tablet/mobile), interações, acessibilidade básica e
fluidez geral. Conclusão: a aplicação **funciona corretamente**, responde bem nos diferentes tamanhos de
tela e a experiência de uso é percebida como **fluida e confortável**. Durante essa revisão foram
identificados e corrigidos dois ajustes finos: (1) centralização dos ícones da barra de navegação
inferior em mobile e (2) remoção de duas imagens decorativas do Hero em mobile (não haviam alterações
em desktop).

**Nota sobre o lint:** os erros restantes são pré-existentes e fora do escopo desta entrega
(`react-hooks/set-state-in-effect` em `src/routes/index.tsx` e `src/routes/checkout.tsx`).
**Nota sobre a suíte completa:** para executar os três viewports localmente é preciso o WebKit
(`npx playwright install webkit`), pois o projeto `chromium-tablet` usa o device iPad (WebKit).

## Pull Request / Entrega

Para preparar a entrega via Git:

```bash
# 1. Confira o estado do repositório (tracked/untracked) e os diffs
git status
git diff

# 2. (Recomendado) Crie uma branch de entrega
git checkout -b entregar-challenge

# 3. Adicione os arquivos desejados e faça o commit
git add .
git commit -m "Entrega do desafio Kurio — Marketplace de NFTs"

# 4. Envie a branch e abra o Pull Request
git push -u origin entregar-challenge
```

O Pull Request deve incluir:

- Resumo do que foi entregue e das decisões principais (arquitetura, mocks, tempo real).
- Como rodar localmente (apontando para a seção de Instalação/Execução deste README).
- Resultados das verificações (typecheck, testes, Lighthouse) e a URL pública.
- Telas/screenshots dos fluxos principais e da responsividade.
- Eventuais limitações conhecidas e o estado dos baselines de regressão visual.

> Este README não executa operações de Git por você — os comandos acima são apenas o procedimento
> recomendado.

## Estrutura do repositório

```
.
├── src/                  # Código-fonte (rotas, componentes, features, lib)
│   ├── routes/           # Rotas por arquivo (home, detalhe, carrinho, checkout…)
│   ├── components/       # Componentes de UI e layout
│   ├── features/         # Lógica por domínio (catálogo, carrinho, checkout…)
│   ├── mocks/            # MSW: handlers, fixtures, cenários, db, realtime
│   └── lib/              # Axios, queryClient, socket, decimal, sessão
├── tests/e2e/            # Testes Playwright + support + snapshots visuais
├── scripts/              # Lighthouse, predeploy-check, gerador de avatares
├── public/               # Worker MSW, imagens/fontes estáticas
├── reports/lighthouse/   # Relatórios e resumo das auditorias
├── docs/                 # Documentação de mocks
├── dist/                 # Build de produção (gerado, ignorado pelo Git)
├── playwright.config.ts  # Configuração dos testes (3 viewports)
├── package.json
├── vercel.json           # Rewrites de SPA para o deploy
└── components.json       # Convenção shadcn/ui
```

## Documentação adicional

- [`ARCHITECTURE.md`](ARCHITECTURE.md) — contratos REST e de eventos, política de sessão, estado do
  carrinho, estratégia de cache/retries, reconciliação REST ↔ Socket.IO, limitações e desvios do Figma.
- [`docs/mocking.md`](docs/mocking.md) — detalhes de ativação, persistência/reset e cenários do MSW,
  incluindo o transporte usado para simular o Socket.IO.