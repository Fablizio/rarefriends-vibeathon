## The Binding of RareFriend

A twin-stick, room-by-room dungeon crawler where your Generations Friend is the hero and every enemy and boss is another real Rare Friend.

**Builder:** Fablizio · [GitHub @Fablizio](https://github.com/Fablizio) · [X @FabrizioCottone](https://x.com/FabrizioCottone) · [Telegram @Fablizio](https://t.me/Fablizio) · **Category:** Character Spotlight · **SDK:** FriendSDK v0.1.2

Your ownership-verified Friend fights as itself, drawn from its canonical on-chain sprite, and its family (one of the nine) gives it a signature perk. The dungeon is built from the collection too: each floor belongs to a family, which sets its look, obstacles and enemy behaviour, and every enemy and boss is a real Generations Friend (token ID on screen) read live from the SDK's pinned artwork registry. Floor 1 is always your own family's turf.

![binding-of-rarefriend demo](https://raw.githubusercontent.com/Fablizio/the-binding-of-rarefriend/a4c1a6859238b4d49008349a0d1175f2434c387d/games/binding-of-rarefriend/media/demo.gif)

*Demo recorded headlessly with SDK sample sprites and a bot at the controls; in play you see your own Friend and live Friends from the chain.*

- **Play:** https://fablizio.github.io/the-binding-of-rarefriend/
- **Source:** https://github.com/Fablizio/the-binding-of-rarefriend/tree/a4c1a6859238b4d49008349a0d1175f2434c387d (game in [`games/binding-of-rarefriend/`](https://github.com/Fablizio/the-binding-of-rarefriend/tree/a4c1a6859238b4d49008349a0d1175f2434c387d/games/binding-of-rarefriend))
- **Wallet and network:** a browser wallet on **Robinhood mainnet (4663)** holding a hardwired Generations NFT (generation ≥ 1). On a phone, open the link in your wallet's in-app browser. The SDK runtime handles connection, Friend selection and the fresh ownership check. No transaction or signature is requested.

## What makes your Friend unique

- **Signature ability** derived from your Friend's own on-chain seed and token ID: one of 8 abilities (Ricochet, Boomerang, Orbit Shard, Chain Spark, Critical Eye, Heart Leech, Trailblazer, Fifth Shot). The same Friend always gets the same one, and it stacks with the family perk and relics.
- **Generation bonus** read from your Friend's own `generation()`: gen 1 +1 heart · gen 2 +15% damage · gen 3 +10% fire rate · gen 4 +10% speed · gen 5 +15% range · gen 6+ +5% damage. If the read fails, the game shows no bonus and plays normally.
- **Story:** "The descent of Friend #ID". On victory the defeated Friends bow ("The crypt remembers Friend #ID"), and there's **Copy result** to share the run on Telegram or X.

## Run it

Node.js 22+ on Linux or Ubuntu/WSL2:

```sh
git clone https://github.com/Fablizio/the-binding-of-rarefriend.git
cd the-binding-of-rarefriend
git checkout a4c1a6859238b4d49008349a0d1175f2434c387d
npm ci
npm run build
npm run dev:game -- games/binding-of-rarefriend
```

Open `http://localhost:4173`, connect your wallet, select your Friend and press **Enter the dungeon**. Static build: `npx friendsdk build games/binding-of-rarefriend` (output in `games/binding-of-rarefriend/.friendsdk/`, served as-is on GitHub Pages).

## Play

| | Keyboard / mouse | Touch (landscape) |
| --- | --- | --- |
| Move | WASD | Left thumb, anywhere on the left half |
| Shoot | Arrow keys, or hold the left mouse button to aim at the cursor | Right thumb, anywhere on the right half |
| Pause / mute | P or Esc / M, or the on-screen buttons | On-screen buttons |

Settings and the pause menu include **Mute** and **Reduce motion** (no shake, fades, bobbing or walk cycles). The run pauses on blur, hidden tabs and whenever the runtime opens its menus. Everything stays inside the SDK container; `host.css` only keeps the 3:2 frame fully visible on landscape phones.

## Rules

- A run is **4 floors**. Each floor is a grid of single-screen rooms with a start room, fights, a **treasure room** and a **boss room**.
- Doors lock until every Friend in the room is defeated. Cleared rooms may drop a heart or a spark.
- Each boss is a real Friend at 6× size with three family attacks, and it speeds up below half health. Beating it gives a relic, a heart and the way down. The fourth boss opens the exit.
- You start with 3 hearts. Hits cost half a heart (bosses a full heart from floor 2), followed by brief invulnerability.
- Ten stackable relics come from treasure rooms and bosses (extra heart, damage, familiar, split shots, fire rate, flight, big shots, range, speed, homing). Sparks are score only.
- The end screen lists every real Friend you faced, by token ID.

**Family perks:** Skeleton piercing shots · Mask twin shots · Family mini-me familiar · Cellular split shots · Asymmetry wobbly +25% damage · Hoverer flight · Colossus +1 heart and huge shots · Sparkling 8-way burst every 6th shot · Hollow longer invulnerability and speed.

**Enemy families:** Skeleton chasers · Mask shooters that blink · Family packs of three that lunge · Cellular split in two · Asymmetry zigzag with diagonal shots · Hoverer fly over obstacles · Colossus charge in straight lines · Sparkling ring bursts · Hollow vanish and reappear beside you.

## How the Friends are chosen

For each run (and on **New cast**), the game samples 120 random token IDs in 1–100,000 and reads their family from the pinned sprite registry (`familyOf`). It groups them into floors and reads `seedOf` and the canonical `frames` of up to six Friends per floor. It uses one Multicall3 call per step, with a fallback to batched individual reads. Only the public artwork registry is read. The Generations collection is never scanned and no owners are looked up. Enemy art is the unmodified canonical 16×16 mask at integer scale with a family-coloured halo.

## Economy

**0 RF. Play is free, with no purchases, consumables, rewards or simulated balances.** Sparks and relics last one run and reset on reload. The v0.1.2 runtime requires a chance-game `game.json`, so the game ships **unused schema-only terms** (a 1 RF token with a single 10,000 bps reward of 1 RF, `1000000000000000000` base units each). The component never calls `buy`, `play`, `settle` or `redeem`. Token Activity metrics are not claimed.

Possible RF integrations, not implemented: an RF-priced second-chance heart, RF-backed cosmetic halos, and a seeded weekly crypt with an RF-funded prize pool. These would need custom integration beyond the v0.1.2 bridge, which has no persistence, upgrade or extra-currency APIs.

## Checks, credits and limitations

- **Passed:**
  - `npx friendsdk check games/binding-of-rarefriend` and `npm run check:games`.
  - `npm test`: 114 passed, 2 skipped.
  - `npm run typecheck` and strict `tsc -p games/binding-of-rarefriend/tsconfig.json`.
  - `npx friendsdk build games/binding-of-rarefriend`.
- **Headless simulation** (`node games/binding-of-rarefriend/tests/run-sim.mjs`): a bot plays 54 full runs across all nine player families. Invulnerable, it clears the whole dungeon in 27 of 27 runs. With normal health it wins 1 of 27 and reaches floor 2.5 on average. Signatures are balanced (average floor reached 2.11–2.89 across the 8, same seeds) and evenly distributed over 20,000 IDs. It doesn't dodge, so this is not a balance measurement.
- **Browser check** (`node games/binding-of-rarefriend/tests/browser.mjs`): the real SDK runtime in headless Chromium with the SDK's mock wallet and RPC fixtures, extended to answer the artwork registry and Multicall3. It covers desktop, phone landscape and phone portrait with no browser errors.
- **Known check failure:** the stock `npx friendsdk test` fixture only answers artwork reads for sample Friend #7730, so it rejects the roster reads for other Friends by design.
- **Real-wallet playtest:** done by the builder with a hardwired Generation 6 Friend.
- **Limitations:** the random cast depends on the public Robinhood RPC; if it is unreachable, the game shows an error with Retry. The ID range 1–100,000 is an assumption about where hardwired Friends live. Portrait phones get a small 3:2 frame, so landscape is recommended.
- **Credits:** code, rooms, props and sound effects by Fablizio (AI-assisted). Scenery is drawn in code and sounds are synthesized. Character art: canonical Rare Friends Generations sprites via the FriendSDK sprite reader; reward cues from the FriendSDK sound kit (see the SDK `NOTICE.md`). Inspired by the room-based twin-stick roguelite genre and not affiliated with any other game. No trading, wearable NFTs, creator fees or live economy. Production publication needs separate Rare Friends review.
