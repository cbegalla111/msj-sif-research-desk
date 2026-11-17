# MSJ Student Investment Fund — Research Desk

**[Open the application →](https://cbegalla111.github.io/msj-sif-research-desk/)**

The interface is hosted on GitHub Pages. Live market-data requests use the public hosted Worker API. Watchlists, notes, and saved models remain in each visitor's browser.

[API-hosted fallback](https://compound-research-desk-m7q2.christianbegalla.chatgpt.site)


Student equity research dashboard. Company selection and watchlists, analyst targets and EPS forecasts, metric-by-metric peer comparison, and an editable unlevered DCF. Watchlists, models, notes, and last-successful snapshots are local to the visitor's browser.

## Build and check

- `npm run build`: embeds the UI, client modules, and API handler into a dependency-free Cloudflare Worker at `dist/server/index.js`.
- `npm test`: financial normalization, valuation, missing-data handling, quote reference, and Worker routing checks. Build first after source changes.

## Data

Worker endpoints `/api/search`, `/api/quote`, and `/api/company` use Yahoo Finance equity search, chart and standardized quarterly timeseries data, plus Nasdaq analyst consensus and company classifications. There are no API secrets in the client. Quotes cache for 60 seconds; financials and EPS for 6 hours; analyst targets for 1 hour. Caches are bounded and isolate-local, so source request frequency can vary across instances. The open visible dashboard refreshes the active quote and watchlist every 60 seconds and reloads company data every 15 minutes. Closing the page stops polling. Source quote time, financial period, and retrieval time are separate fields. Provider failure is visible; stored snapshots are labeled stale.

Supported financial comparisons use USD statements and USD-listed equities. Coverage is provider-dependent. Missing metrics remain unavailable; no fictional financials are substituted. Market-cap and valuation multiples use latest diluted average shares, so they are estimates. ROIC is an explicitly labeled proxy. Analyst EPS growth can optionally become an initial revenue-growth proxy in the DCF; EPS and revenue growth are not equivalent. Price targets remain independent of DCF valuation.

Peer suggestions are curated starting points, not equivalence claims. Metric leaders are computed only within selected companies and do not constitute a buy/sell score. Banks, insurers, and REITs generally require different models.

## Investigation tools

Thirteen deterministic screens cover cash burn, cash conversion, margin changes, capital intensity, leverage, interest coverage, dilution, stock compensation, receivables, inventory, analyst disagreement, and revisions. Every screen distinguishes signals from no trigger and missing coverage, states its threshold, and supplies a follow-up research question. These are broad heuristics and are not fraud detection or investment recommendations.

`/api/research` supplies recent SEC filing links (cached 1 hour) and provider-tagged company news (cached 15 minutes). Curated primary-source briefs for four starter companies carry a fixed November 16, 2026 research date and do not auto-generate new summaries. Live source feeds and dated editorial analysis are separate.

Filing retrieval uses a bundled SEC ticker-to-CIK directory snapshot (November 16, 2026), direct SEC submissions when available, and clearly attributed Nasdaq/QuoteMedia mirrors when direct access fails. Unknown tickers attempt the current SEC directory. Coverage failures retain an EDGAR search link.

## MSJ branding and Info & Data

The navy-and-gold theme and corner crest adapt the user-provided SIF reference. The research watchlist is explicitly not a record of fund holdings. Browser storage keys and the existing origin are retained so existing local models and notes continue to work.

The Info & Data tab documents source lineage, refresh and cache timing, financial methodology, student pitch workflow, and browser-local storage. Normal loaded responses and direct diagnostics have different status labels. `/api/health?symbol=MSFT` runs seven company-specific probes against quotes, standardized statements, targets, EPS estimates, search, filings, and news. These requests bypass the app's response cache but cannot control a provider's own caching. Results distinguish failed requests, missing company coverage, stale snapshots, and untested feeds, with source and retrieval timestamps. They are point-in-time observations, not global provider uptime or a promise of exchange-real-time data.

## Compact views and simultaneous use

Only one research panel is visible at a time. Notes, detailed forecasts, sensitivity, narrative briefs, and explanations are expandable. Information and diagnostics remain in a separate view.

The hosted Worker can process independent user requests; browser-local state is per browser profile, not shared fund data. Identical in-flight upstream requests are coalesced within one Worker instance, alongside existing TTL caching. This reduces duplicate requests during bursts, but is not a cross-instance shared cache. Provider quotas can still limit concurrent usage. No tested maximum user count is claimed. Website audience remains controlled by Sites permissions; public GitHub visibility does not grant website access.

## GitHub Pages deployment

- `npm run build:pages` writes the static frontend to the repository root.
- Repository Pages settings: **Deploy from a branch → main → / (root)**.
- The live URL is https://cbegalla111.github.io/msj-sif-research-desk/.
- `.nojekyll` keeps JavaScript and assets unmodified. Relative module/image paths support the repository subpath.
- `globalThis.MSJ_API_BASE` contains the public API origin, not a credential. The API explicitly permits the GitHub Pages origin through CORS.
- After frontend changes, rebuild Pages output and push `main`. GitHub then publishes the new files. Worker/backend changes additionally require a Sites deployment; a GitHub push alone does not deploy the API.
- The API serves public financial data only. It does not store or expose visitors' watchlists or notes.
