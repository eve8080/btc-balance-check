# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

btc-balance-check: a tool to check Bitcoin address balances. Started 2026-10-27. iPhone web page for BTC (xpub) and ETH balances, hosted on GitHub Pages from `main`: https://eve8080.github.io/btc-balance-check/ (public repo eve8080/btc-balance-check). Pushing to `main` deploys. Commit only when the owner asks.

The owner often works from an iPhone through Remote Control: keep replies short and give clear next steps.

## Commands

No build step. Run locally: `python3 -m http.server 8765`, then open http://localhost:8765/index.html.

## Architecture

Single static page, `index.html`, built for iPhone Safari. Libraries load as ES modules from cdn.jsdelivr.net (@scure/bip32, @scure/base, @noble/hashes), pinned to exact versions.

- BTC: an xpub/ypub/zpub is normalised to xpub version bytes, then receive (0) and change (1) addresses are derived until 20 unused in a row (gap limit). Ledger shows native segwit keys as `xpub`, so the address type is a separate per-account setting. Balances come from blockchain.info `/balance?active=a|b|…&cors=true`, 40 addresses per chain per request (both chains in one call). If that fails, the page falls back to per-address Esplora lookups, alternating mempool.space and blockstream.info. Both rate-limit after about 20 quick requests, and their rate-limit replies lack CORS headers, so network errors are retried.
- ETH: `eth_getBalance` via ethereum-rpc.publicnode.com. Native ETH only, no tokens yet.
- Prices: CoinGecko simple/price.
- Accounts are saved in the browser's localStorage only. The last balances and prices are cached there too (`wallet-balance-v1-cache`) so the page shows numbers instantly. All accounts refresh in parallel.

## Rules

- No tokens, passwords or API keys in any file.
- Never ask for, store or log private keys or seed phrases. The tool only needs public addresses (or xpubs).
