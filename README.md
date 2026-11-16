# Compound Research Desk

Private personal equity research dashboard. Company selection and watchlists, analyst targets and EPS forecasts, metric-by-metric peer comparison, and an editable unlevered DCF. Watchlists, models, notes, and last-successful snapshots are local to the visitor's browser.

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
