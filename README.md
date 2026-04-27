# topo — Vendry Sync Server v14.1

Bridge Node.js entre Cloudflare Workers e a Shopee Brasil, usando Bright Data Scraping Browser + proxy residencial pra contornar bloqueio de IP de datacenter.

Cobertura: Order, Product, Logistics, Shop, Finance, Promotion, Analytics, Returns, Label, Voucher, Account, Chat, Fulfillment, Inventory, Category, Rating, Search, Recommend.

## Stack

- Node.js puro (>=18) — apenas módulos nativos (`http`, `https`, `url`)
- Sem dependências externas no `package.json`
- Pronto pra Railway (detecta automaticamente)

## Variáveis de ambiente

| Nome | Obrigatório | Descrição |
|---|---|---|
| `PORT` | não (default 3000) | Porta HTTP do server |
| `SYNC_SECRET` | sim | Token compartilhado com os clients (Cloudflare Workers) |
| `BD_WSS` | sim | URL WebSocket do Bright Data Scraping Browser. Formato: `wss://USER:PASS@brd.superproxy.io:9222` |

Configure no Railway em **Variables**.

## Deploy no Railway

1. Conectar este repo GitHub ao Railway (deploy automático)
2. Configurar `SYNC_SECRET` e `BD_WSS` em Variables
3. Railway detecta `package.json`, roda `npm start`
4. Service ficará exposto em algo como `https://topo-production-xxxx.up.railway.app`

## Endpoints principais

O server expõe múltiplos endpoints organizados por categoria. Todos exigem header `X-Sync-Secret: $SYNC_SECRET`.

Documentação completa nos comentários do `index.js` (linhas iniciais e blocos `// ══` por categoria).

## Clientes conhecidos

- `winner-hunter` (worker `godmode` em https://godmode.sospwa011.workers.dev) — Fase 2+ usará este bridge para fetch de top products
- Vendry v2 (worker `import`) — uso original do bridge

## Histórico

- v14.1 — refresh hierarchy fix (L1 → L2 → L3), validação real de tokens, 5 endpoints de refresh
- Gerador automático de paths × versões × variações pra cobertura total da API Shopee
- IA adaptativa: 8 UAs, header rotation, response classifier (BLOCKED, RATE_LIMITED, AUTH_REQUIRED, OK)
