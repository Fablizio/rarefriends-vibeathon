## The Binding of RareFriend

A twin-stick, room-by-room dungeon crawler where your Generations Friend is the hero and every enemy and boss is another real Rare Friend.

**Builder:** Fablizio · [GitHub @Fablizio](https://github.com/Fablizio) · [X @FabrizioCottone](https://x.com/FabrizioCottone) · [Telegram @Fablizio](https://t.me/Fablizio) · **Category:** Character Spotlight · **SDK:** FriendSDK v0.1.4

Your ownership-verified Friend fights as itself, drawn from its canonical on-chain sprite, and its family (one of the nine) gives it a signature perk. The dungeon is built from the collection too: each floor belongs to a family, which sets its look, obstacles and enemy behaviour, and every enemy and boss is a real Generations Friend (token ID on screen) read live from the SDK's pinned artwork registry. Floor 1 is always your own family's turf.

![binding-of-rarefriend demo](https://raw.githubusercontent.com/Fablizio/the-binding-of-rarefriend/e3f5894b21eff723cc22808cc36ac096ddc1c4ac/games/binding-of-rarefriend/media/demo.gif)

*Demo recorded headlessly with SDK sample sprites and a bot at the controls; in play you see your own Friend and live Friends from the chain.*

- **Play:** https://fablizio.github.io/the-binding-of-rarefriend/
- **Source:** https://github.com/Fablizio/the-binding-of-rarefriend/tree/e3f5894b21eff723cc22808cc36ac096ddc1c4ac (game in [`games/binding-of-rarefriend/`](https://github.com/Fablizio/the-binding-of-rarefriend/tree/e3f5894b21eff723cc22808cc36ac096ddc1c4ac/games/binding-of-rarefriend))
- **Wallet and network:** a browser wallet on **Robinhood mainnet (4663)** holding a hardwired Generations NFT (generation ≥ 1). On a phone, open the link in your wallet's in-app browser. The SDK runtime handles connection, Friend selection and the fresh ownership check. No transaction or signature is requested.

## What makes your Friend unique

- **Signature ability** derived from your Friend's own on-chain seed and token ID: one of 8 abilities (Ricochet, Boomerang, Orbit Shard, Chain Spark, Critical Eye, Heart Leech, Trailblazer, Fifth Shot). The same Friend always gets the same one, and it stacks with the family perk and relics.
- **Generation rank** read from your Friend's own `generation()`, a ladder where rarer is stronger: Gen 1 Legendary +1 heart, +15% damage, +10% fire rate and a gold outline · Gen 2 Epic +1 heart, +10% damage · Gen 3 Rare +10% damage, +5% fire rate · Gen 4 Uncommon +10% damage · Gen 5 Common +5% damage · Gen 6+ Standard no bonus (label only). The tier shows on the title card, the boss VS card and the HUD. If the read fails, the game shows no bonus and plays normally. Genesis NFTs are a separate collection that FriendSDK v0.1.4 cannot select as a player; a Genesis-holder perk is on the roadmap.
- **Boss VS card:** your Friend against the floor's keeper (a real Friend), both large with their numbers, before every boss fight.
- **Chiptune soundtrack** generated in code, one theme per family floor, with a boss variant, a victory jingle and a separate Music toggle.
- **An elite Friend per floor** (4× size, one extra family move, a guaranteed reward) and 31 room layouts, including family-themed ones.
- **Story:** "The descent of Friend #ID". On victory the defeated Friends bow ("The crypt remembers Friend #ID"), and there's **Copy result** to share the run on Telegram or X.

## Run it

Node.js 22+ on Linux or Ubuntu/WSL2:

```sh
git clone https://github.com/Fablizio/the-binding-of-rarefriend.git
cd the-binding-of-rarefriend
git checkout e3f5894b21eff723cc22808cc36ac096ddc1c4ac
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

**0 RF. Play is free, with no purchases, consumables, rewards or simulated balances.** Sparks and relics last one run and reset on reload. The v0.1.4 runtime requires a chance-game `game.json`, so the game ships **unused schema-only terms** (a 1 RF token with a single 10,000 bps reward of 1 RF, `1000000000000000000` base units each). The component never calls `buy`, `play`, `settle` or `redeem`. Token Activity metrics are not claimed.

Possible RF integrations, not implemented: an RF-priced second-chance heart, RF-backed cosmetic halos, and a seeded weekly crypt with an RF-funded prize pool. These would need custom integration beyond the v0.1.4 bridge, which has no persistence, upgrade or extra-currency APIs.

## Checks, credits and limitations

- **Passed:**
  - `npx friendsdk check games/binding-of-rarefriend` and `npm run check:games`.
  - `npm test`: 116 passed, 2 skipped.
  - `npm run typecheck` and strict `tsc -p games/binding-of-rarefriend/tsconfig.json`.
  - `npx friendsdk build games/binding-of-rarefriend`.
- **Headless simulation** (`node games/binding-of-rarefriend/tests/run-sim.mjs`): a bot plays 54 full runs across all nine player families. Invulnerable, it clears the whole dungeon in 27 of 27 runs. With normal health it wins 1 of 27 and reaches floor 2.4 on average. On the same 18 seeds it reaches floor 2.83 on average as Gen 1 (Legendary) versus 1.94 as Gen 6. All 31 layouts are connected and every floor has an elite room. Signatures are balanced (average floor reached 2.00–2.56 across the 8, same seeds). It doesn't dodge, so this is not a balance measurement.
- **Browser check** (`node games/binding-of-rarefriend/tests/browser.mjs`): the real SDK runtime in headless Chromium with the SDK's mock wallet and RPC fixtures, extended to answer the artwork registry and Multicall3. It covers desktop, phone landscape and phone portrait with no browser errors.
- **Known check failure:** the stock `npx friendsdk test` fixture only answers artwork reads for sample Friend #7730, so it rejects the roster reads for other Friends by design.
- **Real-wallet playtest:** done by the builder with a hardwired Generation 6 Friend.
- **Limitations:** the random cast depends on the public Robinhood RPC; if it is unreachable, the game shows an error with Retry. Enemies are sampled from IDs 1–100,000 only; hardwired Friends also exist above that range (e.g. #332833 is a Gen 6). Portrait phones get a small 3:2 frame, so landscape is recommended.
- **Credits:** code, rooms, props and sound effects by Fablizio (AI-assisted). Scenery is drawn in code and sounds are synthesized. Character art: canonical Rare Friends Generations sprites via the FriendSDK sprite reader; reward cues from the FriendSDK sound kit (see the SDK `NOTICE.md`). Inspired by the room-based twin-stick roguelite genre and not affiliated with any other game. No trading, wearable NFTs, creator fees or live economy. Production publication needs separate Rare Friends review.
