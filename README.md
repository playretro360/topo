# topo — Vendry Sync Server v14.1

Bridge Node.js entre Cloudflare Workers e a Shopee Brasil, usando Bright Data Scraping Browser + proxy residencial pra contornar bloqueio de IP de datacenter.

**Diferencial v14.1**: força IP de saída brasileiro injetando `-country-br` automaticamente no username do Bright Data, garantindo que toda request vai pra Shopee parecendo originar do Brasil.

Cobertura: Order, Product, Logistics, Shop, Finance, Promotion, Analytics, Returns, Label, Voucher, Account, Chat, Fulfillment, Inventory, Category, Rating, Search, Recommend.

## Stack

- Node.js >=18 puro
- Dependência única: `ws` (WebSocket client pra Bright Data CDP)
- Pronto pra Railway (detecta automaticamente, roda `npm start`)

## Variáveis de ambiente

### Obrigatórias

| Nome | Descrição |
|---|---|
| `SYNC_SECRET` | Token Bearer que clientes (Cloudflare Workers) precisam enviar em `Authorization: Bearer <SYNC_SECRET>` |

### Bright Data (pelo menos 1 dos 3 grupos)

**Grupo A — Scraping Browser (CDP via WebSocket, recomendado):**
- `BD_WSS` — `wss://USER:PASS@brd.superproxy.io:9222`
  - Geo-targeting BR é **automático**: o server injeta `-country-br` no username
  - Não precisa configurar `-country-br` no dashboard Bright Data, o server faz isso

**Grupo B — Web Unlocker (HTTP API):**
- `BD_API_TOKEN` — token API geral da conta Bright Data
- `BD_UNLOCKER_KEY` — chave da zona Web Unlocker

**Grupo C — Residential Proxy (HTTP/HTTPS proxy):**
- `BD_PROXY_HOST` — `brd.superproxy.io:22225`
- `BD_PROXY_USER` — `brd-customer-XXX-zone-residential:SENHA`

### Automática

- `PORT` — Railway define automaticamente

## Deploy no Railway

1. Conectar este repo GitHub ao Railway (deploy automático)
2. Configurar `SYNC_SECRET` + variáveis Bright Data em **Variables**
3. Railway detecta `package.json`, instala `ws`, roda `npm start` (= `node server.js`)
4. Service ficará exposto em algo como `https://topo-production-XXXX.up.railway.app`

## Endpoints principais

Todos os endpoints exigem header `Authorization: Bearer $SYNC_SECRET`. Sem ele, retorna 401.

- `GET /health` — status do server, breakers ativos, top endpoints, estatísticas de geração

Mais de 2.500 endpoints elásticos gerados dinamicamente por categoria. Documentação completa nos comentários do `server.js`.

## Clientes conhecidos

- `winner-hunter` (worker `godmode`) → https://godmode.sospwa011.workers.dev — Fase 2+ usará este bridge para fetch de top products da Shopee BR

## Histórico

- v14.1 — refresh hierarchy fix (L1 → L2 → L3), validação real de tokens, 5 endpoints de refresh, **geo-targeting BR automático**
- IA adaptativa: 8 UAs, header rotation, response classifier (BLOCKED, RATE_LIMITED, AUTH_REQUIRED, OK)
- Gerador automático de paths × versões × variações pra cobertura total da API Shopee
