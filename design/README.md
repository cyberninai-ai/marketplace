# Market — design handoff

A multi-chain NFT marketplace prototype that combines OpenSea's breadth (many chains, all categories, easy onboarding) with Blur's dense, trader-first look. It covers collecting and trading, creating and launching collections (allowlists, mint phases, deploy, go live), your own space, people and organizations, and every error state.

**Status:** clickable prototype, not production code. Layout, flows, copy, interactions and tokens are set. Brand, real data and smart-contract details are still open (see [Open decisions](#open-decisions)).

- **Live canvas (view and click through):** https://claude.ai/artifact/5UPhceQSUH7ZrnSfEPs3uv. It's private until the owner shares it from the canvas's Share menu.
- **Source of each page:** `design/prototype/*.dc.html` (one file per page) and `design/prototype/canvas.json` (board layout).
- **Tokens:** `design/tokens.json`.

> The `.dc.html` files need the canvas runtime (`support.js`) to render, so open them on the canvas, not straight from disk. They're still readable HTML: markup, copy and inline styles are final-intent. The `<script type="text/x-dc">` block at the bottom of each file holds that page's sample data and interaction state, so it's the best reference for behavior.

---

## 1. Page inventory

| # | Page | File | Suggested route | Purpose |
|---|------|------|-----------------|---------|
| 1 | Discover (bento) | `Main.dc.html` | `/` | Featured live drop, trending table (1h/24h/7d/30d), minting now, upcoming drops, your allowlists, floor movers, categories, create CTA. Chain filter on top. |
| 2 | Collection + sweep | `Collection.dc.html` | `/c/:slug` | Stats strip, trait filters, dense item grid with multi-select, sticky **sweep** bar (slider = N cheapest). |
| 3 | Basket → sign → done | `Cart.dc.html` | `/basket` | Review → wallet confirm → success, all in one page. One transaction for all items. "Substitute if sold" option. |
| 4 | Create collection → go live | `Create.dc.html` | `/create` | 7-step creator wizard (below). |
| 5 | Drop / mint page | `Mint.dc.html` | `/drop/:slug` | Phase list, allowlist eligibility, quantity, mint, progress. |
| 6 | List items | `Sell.dc.html` | `/list` | Bulk list with one signature; Floor / Floor+5% / Top-offer presets; below-floor warning; fee breakdown. |
| 7 | My assets | `Portfolio.dc.html` | `/me` | Value, P&L, items/offers/listings/activity/hidden tabs, multi-select bulk actions. |
| 8 | Watchlist + alerts | `Watchlist.dc.html` | `/me/watchlist` | Watched collections, alert toggles, create alert (push/email/Telegram). |
| 9 | Public profile | `Profile.dc.html` | `/u/:handle` | Artist/collector profile; Created/Collected/Activity/Communities/Exhibitions. |
| 10 | Settings | `Settings.dc.html` | `/settings/:section` | Profile, linked wallets (multi-chain), notifications, offers & trading, security, display. |
| 11 | Artists | `Artists.dc.html` | `/artists` | Spotlight, rising, discipline filters, directory with follow. |
| 12 | Communities | `Communities.dc.html` | `/communities` | Token-gated groups, eligibility per wallet, "buy at floor to join", events. |
| 13 | Causes | `Causes.dc.html` | `/causes` | Cause drops (split %, progress), direct donation, verified partner orgs. |
| 14 | Institutions | `Institutions.dc.html` | `/institutions` | Verified museums/galleries/foundations/brands, "For institutions" lead form. |
| 15 | 404 | `NotFound.dc.html` | `*` | Search plus suggestions. |
| 16 | Errors, empty & loading | `States.dc.html` | — (component sheet) | Every error, empty, toast and skeleton state, with copy. |

Canvas rows: **Collect & trade** (1–3), **Create & launch** (4–6), **Your space** (7–10), **People & organizations** (11–14), **Errors & states** (15–16).

## 2. Core flows

**Buy (sweep)**
Discover → Collection → select items or drag the sweep slider → Basket → sign once → Done → "List them".
- Items that sell before signing are skipped and not charged, or swapped for the next-cheapest when "substitute" is on.
- Fees show before signing. Buying has 0 marketplace fee; gas is shown as an estimate.

**Mint (collector)**
Discover tile / Drops → Drop page → connect wallet → eligibility ("You're on the allowlist · 2 of 2 left") → quantity → Mint → wallet confirm → minted.
- The phase list always shows the live phase with its countdown and the next phase with its start time.
- Spots left update after each mint. Once spots run out, the page points to the public phase or to listing.

**Create → launch (creator)**: `Create.dc.html`, left-rail stepper. Steps can be revisited; Live unlocks only after deploy.
1. **Details**: name, symbol (≤8, uppercased), description, logo, banner, category, links.
2. **Chain & contract**: Ethereum / Base / Arbitrum / Polygon (ERC-721A or ERC-1155), Solana (Metaplex Core / cNFT), Bitcoin (Ordinals). Supply, royalty % (max 10), payout address (multisig hint).
3. **Artwork**: media folder/zip, traits CSV (template download), storage (IPFS / Arweave / on-chain), reveal: instant or delayed (placeholder image + reveal trigger).
4. **Mint phases**: ordered list; each phase has name, who can mint (Allowlist / Holders / Public), start, price, per-wallet limit. Add/remove; a timeline bar previews the order.
5. **Allowlist**: sources are Paste/CSV, Holder snapshot, or Claim page. Live validation counts valid, invalid and duplicate entries; ENS accepted; optional per-address limit (`addr,limit`). Stored as a Merkle root; shows which phases it applies to.
6. **Review & deploy**: summary, then 4 signed transactions shown as a checklist (Deploy contract → Upload metadata → Set allowlist root → Configure phases), each Pending → Confirm in wallet → Confirmed.
7. **Live**: status (Live/Paused), minted, revenue, unique minters, current phase plus next-phase countdown, phase timeline, then Pause/Resume, Edit phases & allowlist, Reveal now, Withdraw, Copy mint link, View drop page.

**List (seller)**
My assets → select → List selected → set prices (presets or per item) → one signature → Listed.
- Listing is gasless (signed order).
- A below-floor price shows an inline warning, not a block.
- The summary shows listed total, 0.5% marketplace fee, creator royalty and "You receive".

## 3. Visual system

Full values are in `design/tokens.json`.

- **Look:** near-black ground, flat surfaces, 1px borders, one orange accent. No gradients and no shadows. Dense tables and grids, as in Blur.
- **Type:** Geist for UI. **Geist Mono with tabular numbers for every number**: prices, %, counts, addresses, hashes, countdowns. Numbers must not jitter when they update.
- **Accent** (`#FF8A00`) is reserved for: primary action, selection, the active tab underline, and progress.
  - There is one primary (orange) button per view.
  - Secondary actions are outlined.
- **Price movement:** green up / red down, always with a `+` or `−` sign so color isn't the only signal.
- **Bento grid:** 12 columns with a 12px gap. Tiles span 3–8 columns and collapse to full width below 900px.
- **Header:** sticky. Logo, primary nav, search (`/` shortcut hint), gas indicator, Watchlist star, basket count, wallet chip (opens My assets).

## 4. Components to build

These come up repeatedly across pages and should become the component library:

- AppHeader (with active nav state) and WalletChip
- ChainFilter: pill group
- SegmentedControl: time range, tabs-in-a-box
- Tabs: underline style
- Tile / Card, including the bento variant
- StatStrip: label over mono value
- RankingTable with sparkline
- NFTCard: art, rank badge, price; selectable with an orange ring and checkbox
- SweepBar: sticky; slider, count, total, average, CTA
- ProgressBar: minted / raised
- PhaseCard and PhaseTimeline
- Stepper: wizard rail with done ✓ / current / upcoming
- TxChecklistRow: pending / awaiting signature / confirmed
- AllowlistEditor: textarea plus validation chips
- QuantityStepper
- PriceInput: shows delta vs floor
- FeeSummary
- Switch: `role="switch"`
- Toast: success / pending / error / info
- StateCard: error / warning / info / empty; title, body, optional error code, primary and secondary actions
- Skeleton: NFT card and table row
- Empty states

## 5. States and copy rules

`States.dc.html` is the reference. Every message has to say:
1. what happened,
2. why, if known,
3. the single next action, as the primary button,
4. and an optional second action, outlined.

Funds language must be explicit: "Nothing was sent", "No ETH was spent besides the fee", "you paid for 3".

Cases covered:
- **Wallet:** not connected, wrong network (one-click switch), not enough funds (bridge or buy with card), wallet unresponsive.
- **Transactions:** rejected by user, failed (with error code and explorer link), slow (speed up), signature expired.
- **Market:** item sold (auto-suggest a swap), not on the allowlist (show when public opens), mint limit reached, sold out (go to secondary), mint paused, flagged/stolen item (trading disabled).
- **Empty:** no items, no results, empty watchlist. **Offline:** prices may be stale.
- **Toasts and skeletons:** see the bottom of the sheet.

## 6. Accessibility (already in the markup; keep it)

- Real `<button>`, `<a href>`, `<input>` + `<label>`; no click handlers on divs.
- Icon-only buttons have `aria-label`.
- Toggle chips use `aria-pressed`, switches use `role="switch"` + `aria-checked`, and tabs use `role="tab"` + `aria-selected`.
- Progress bars use `role="progressbar"` with values. Quantity changes use `aria-live`.
- Touch targets are ≥40px (most ≥44px). Muted text (`#8C8F98`) is the lightest allowed on `#0B0B0C`.
- Every page works at phone width: rows wrap, the bento collapses, and wide tables scroll inside their box.
- Visible `:focus-visible` ring in accent.

## 7. Notes for engineering

These are suggestions taken from what the screens need, not decisions.

- **Data objects the UI assumes:**
  - Chain
  - Collection: stats, traits, royalty, contract, standard
  - Item: id, rank, traits, owner, listing, offers
  - Listing: signed order, price, expiry
  - Offer
  - Drop with Phases: access type, start/end, price, per-wallet limit, allowlist root
  - AllowlistEntry: address/ENS, limit
  - Wallet: multiple per user, multi-chain
  - Alert: target, condition, channels
  - Community: gate rule
  - Cause: partner org, split %, payout address
  - Institution: kind, verified
  - Activity events
- **Real-time:** floors, % change, mint progress, phase countdowns, gas and listing availability should stream (websocket/SSE). Basket items need a re-check right before signing.
- **Wallet & chains:**
  - EVM chains (Ethereum, Base, Arbitrum, Polygon) share one connector with network switching.
  - Solana and Bitcoin need their own connectors (e.g. Phantom, Xverse).
  - A user can link several wallets.
- **Allowlist:** Merkle tree over `(address, limit)`. The drop page needs a proof lookup by address, which also powers "Check another wallet".
- **Bulk actions:** sweep buys in one transaction through a router contract. Bulk listing is one signature over many orders.
- **Never show seed-phrase prompts.** Contract approvals must be reviewable and revocable (Settings › Offers & trading).

## 8. Open decisions

- [ ] **Brand:** name ("Market" is a placeholder), logo, final accent color, typeface licensing (Geist is OFL).
- [ ] **Fees:** 0.5% listing fee and 0% buy fee are assumptions. Royalty enforcement policy per chain is undecided.
- [ ] **Chains at launch:** all six are designed; pick the launch set.
- [ ] **Contract stack:** ERC-721A vs. 1155 defaults; router/order protocol; Solana and Ordinals launch tooling.
- [ ] **Causes:** partner verification process, payout transparency page (the "View payouts" link isn't designed).
- [ ] **Institutions:** custody/KYC requirements behind the lead form.
- [ ] **Placeholders to fill:** all `[Bracketed]` text (artist, creator, partner and institution names, banners, raised/goal amounts). All collection names, prices and volumes are **sample data**.

## 9. Not designed yet (suggested next)

- Item detail page: offers, price history, traits, provenance
- Activity / notifications feed
- Search results with autocomplete
- Wallet connect / sign-in modal
- Make-offer and collection-offer flow
- Auctions (shown only as a badge on Profile)
- Mobile-specific navigation (currently the header wraps; a bottom tab bar is the likely answer)
- Light theme
- Analytics / Holders tabs on Collection (the tabs exist; their content doesn't)
