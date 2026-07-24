# Indian Village Tycoon — Feature Reference

Single-file HTML5 canvas farming game (`index.html`, vanilla JS, zero deps, procedural graphics + Web Audio). Live: https://indian-village-tycoon.vercel.app
Deploy: `cd /Users/rohit.wadhwa/indian-village-tycoon && vercel --prod --yes` (no git repo).

Last updated: 2026-06-23.

---

## Core loop
Harvest crops → process them via animals/sheds → collect goods → sell at the मंडी (mandi) truck or fill customer orders → earn ₹ + XP → buy upgrades, workers, land → repeat. Isometric world, drag/joystick or WASD/arrows to move.

---

## Supply chain
**Tier 1 — crops** (walk through ripe/golden crop to harvest into 🎒 bag):
- गेहूँ wheat 🌾 (+भूसा bhusa 🌿), grow 8s
- धान rice 🍚 (+bhusa), grow 9s
- गन्ना cane 🎋, grow 13s
- बैंगन baingan 🍆, grow 7s (high raw value ₹42)

**Tier 2 — animals (must be fed):**
- Murgi Coop 🐔: wheat/rice → 🥚 egg (5s, cap 4)
- Bhains buffalo 🐃: bhusa → 🥛 milk (6s, cap 4) + 💩 gobar (70% chance)
- Animals show a **"Feed 🌾 🍚 / 🌿" hint** when empty. No feed = no output.
- Coop renders multiple hens; buffalo pen renders multiple buffalo (count scales with capacity).

**Tier 3 — advanced:**
- Aam (mango) tree 🥭: passive, 11s, cap 6 (unlocked by default; 2nd tree via West Orchard plot)
- Biogas: gobar → 🔥 gas (7s, cap 5) — LOCKED, unlock ₹140
- Mithai shop: milk + cane + mango → 🍬 mithai (9s, cap 6) — LOCKED, unlock ₹200. Multi-input; on-building slot icons show which inputs are still needed.

**Selling:**
- मंडी Mandi truck (redesigned side-view lorry): stand on it to sell whole bag at the live market price. Triggers order checks.
- Doodhwala 🏍️ milkman: drives along the **road at the top**; sell milk to him at a premium ₹70 (wide buy-range so you can sell from the top fence).
- Godown 🏚️ (storage, 40 slots): walk in carrying → store; walk in empty → retrieve. **"💰 Sell All from Godown"** button in the 🗺️ drawer sells the whole warehouse at once (instant credit + fills orders).

**Base sell prices** (before market ×): wheat 6, rice 7, cane 10, baingan 42, bhusa 3, egg 18, milk 38, gobar 2, gas 28, mango 32, mithai 160.

---

## Workers (max 2, hire pad ₹120 escalating)
- AI: harvest ripe crops, collect machine outputs, deposit inputs to machines.
- Workers NEVER sell at mandi (player keeps that agency).
- Mithai balance check stops one ingredient overfilling.
- Surplus / finished goods they can't route are **deposited into the Godown** (capped at 40; if full they hold the items and idle — never deleted).

## Orders board (Township-style)
- Up to 3 active, named customers (Ramesh Kaka, Sunita Didi, Sarpanch Ji, Bindu Mausi, Raju Bhaiya, Doodhwala, Panditji, Schoolmaster).
- Goods shown with **emoji icons**. Reward ₹ + XP, countdown with fresh/urgent/expiring color.
- **Collapsible** via the "📋 ऑर्डर N ▼/▲" tab (state persists).
- Selling consumes goods per order — one bag can't double-pay multiple orders.

## Weather & market
- Cycle clear → cloudy → rain → clear. Market multiplier 0.7–1.5.
- Clear: high prices (drift 1.35), sun disc + rays, crows active.
- Cloudy: stable (~1.0), cloud puffs.
- Rain: low prices (~0.78), crops grow 1.6×, no crows, heavy rain visuals + ambient sound.
- Banner announces changes with a strategy tip.

## Land expansion & upgrades
- Plots (walk onto glowing pad or buy in drawer): South Fields ₹300 (lv3, cane), East Plots ₹500 (lv5, baingan), West Orchard ₹700 (lv7, 2nd mango). Auto-unlock on reaching the level.
- Pads (all show **label + price always**, edge-triggered): Hire Worker, Bigger Bag (+4, max 40), Scarecrow (aura r170, max 3), Biogas/Mithai unlocks, slot upgrades for Coop/Bhains/Mithai (+2 each, max 12).
- Scarecrows scare crows away.

## XP & levels
- `xpForLevel(l)=80*l*l`. XP from selling, orders, upgrades, plots, workers.
- Level-up auto-unlocks the next available plot (only claims "unlocked" when one actually does).

## Retention systems
- **Daily streak** (7-day): ₹50 / ₹120 / 🌱 Seeds (50% ripen) / ₹300 / ⚡ Boost (2× processing 5 min) / ₹500 / 🎁 ₹1000. Resets if a day is missed.
- **Idle/offline earnings**: on return, capped 10 min, workers × ₹4/s × market. Uses a real quit timestamp for accuracy.
- **Achievements** (8 tiered: First Harvest, Green Thumb, Master Farmer, First Sale, Trader, Helpful Hand, Village Hero, Big Earner) — auto-reward ₹ on unlock, listed in the drawer with a lifetime-stats line.
- **Next-goal HUD chip** 🎯: shows your cheapest next upgrade; tap to open the drawer.

## Onboarding & accessibility
- **Guided tutorial** (first session): bouncing arrows — harvest → sell at mandi → tap orders tab. Runs once.
- **How to Play** modal (bilingual) via `?` and Settings. Auto-shows first play.
- Emoji icons on all goods (readable by non-readers).

## Settings (⚙️ panel)
- Sound effects on/off (procedural SFX).
- Background music on/off (gentle sparse pentatonic ambient).
- **Kids / Family mode**: crows become non-destructive (no crop loss) and orders never expire — zero frustration.
- How to Play, Restart, Hard Refresh (moved out of the always-on bar so kids don't wipe progress by accident).

## Audio (Web Audio, all procedural, respects mute)
- SFX: harvest, pick, sell, deposit, buy, nope, crow caw.
- Ambient animal sounds: gentle 🐄 moo + 🐔 cluck every ~11–20s (only if coop/buffalo unlocked).
- Rain ambient loop. Optional background music.

## Save / load
- localStorage key `ivt_v3`, autosave every 5s. Persists money, xp, level, mute, music, kids mode, stats, achievements, pack, machines, pads, plots, workers, scarecrows, player pos.
- Numeric fields coerced on load (corrupt save can't produce NaN money / frozen movement).
- `ivt_streak` (daily streak), `ivt_quit_ts` (idle timer), `ivt_howto`, `ivt_tutorial`, `ivt_orders_collapsed` flags.

---

## Bug-fix log (do not regress)
- Order cards rendered at top covering the game → `#orders`/`#ordersWrap` pinned bottom with plain `bottom:56px` (the `max(..,env()+px)` form failed to evaluate → fell to auto/top).
- Mute button had **no click handler** → added; icon now flips 🔊⇄🔇.
- Pads showed only price at distance → now always show label + price.
- Workers dropping at godown **deleted** items → now actually stored (fill bar updates, retrievable).
- Milkman cut through fields → first moved outside fence (looked disconnected) → now drives on the road with widened buy-range.
- Duplicate order payout (one bag filled multiple orders off a shared snapshot) → goods consumed per fill.
- Restart leaked boost/milkman/timers → full reset in `newGame`.
- Mouse drag kept walking if released off-window → `blur` handler.
- `chooseDest` could crash if godown missing → guarded.
- Mandi was an unreadable iso box blob → redrawn as a proper side-view lorry.
- Only one hen/buffalo shown → multiple, scaling with capacity.

## Known not-yet-built (audit backlog)
Mandi sell-confirmation w/ itemized preview, welcome-back idle overlay, festival/seasonal event, off-screen objective indicators / minimap, day-night cycle, order/weather persistence across reload, optional mini-game.
