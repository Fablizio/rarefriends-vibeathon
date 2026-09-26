## Daily Crypt

A daily time-attack dungeon, the same for everyone. Every ranked attempt costs $RAREFRIENDS: 20% is burned and 80% funds the prize pool for the day's three fastest verified runs.

**Builder:** Fablizio · [GitHub @Fablizio](https://github.com/Fablizio) · [X @FabrizioCottone](https://x.com/FabrizioCottone) · [Telegram @Fablizio](https://t.me/Fablizio) · **Category:** Token Activity · **SDK:** FriendSDK v0.1.2

Your ownership-verified Generations Friend runs a crypt of 10 rooms in a line. The rooms, the real Rare Friends inside them, their spawns and the power-up offer are generated from the UTC date, so every player faces exactly the same crypt. The lowest time wins, and each hit adds 5 seconds. Spending is the core loop: getting faster means paying for another attempt, and every attempt burns RF.

- **Play:** https://fablizio.github.io/daily-crypt/
- **Source:** https://github.com/Fablizio/daily-crypt/tree/6195607d26709a8a1df5ee74a9a280712bbe88e9 (game in [`games/daily-crypt/`](https://github.com/Fablizio/daily-crypt/tree/6195607d26709a8a1df5ee74a9a280712bbe88e9/games/daily-crypt))
- **Economy design:** [`ECONOMY.md`](https://github.com/Fablizio/daily-crypt/blob/6195607d26709a8a1df5ee74a9a280712bbe88e9/games/daily-crypt/ECONOMY.md)
- **Wallet and network:** a browser wallet on **Robinhood mainnet (4663)** holding a hardwired Generations NFT (generation ≥ 1). On a phone, open the link in your wallet's in-app browser. The SDK runtime handles connection, Friend selection and the fresh ownership check. No transaction or signature is requested.

## Run it

Node.js 22+ on Linux or Ubuntu/WSL2:

```sh
git clone https://github.com/Fablizio/daily-crypt.git
cd daily-crypt
git checkout 6195607d26709a8a1df5ee74a9a280712bbe88e9
npm ci
npm run build
npm run dev:game -- games/daily-crypt
```

Open `http://localhost:4173`, connect your wallet, select your Friend, and choose **Ranked run** or **Practice** in the lobby. Static build: `npx friendsdk build games/daily-crypt` (served as-is on GitHub Pages).

## Play

| | Keyboard / mouse | Touch (landscape) |
| --- | --- | --- |
| Move | WASD | Left thumb, anywhere on the left half |
| Shoot | Arrow keys, or hold the left mouse button | Right thumb, anywhere on the right half |
| Power-up | 1 / 2 / 3, or click | Tap a card |
| Pause / mute | P or Esc / M, or the on-screen buttons | On-screen buttons |

Settings and pause include **Mute** and **Reduce motion**. The clock and the run pause on blur, hidden tabs, the pause menu and the runtime's menus.

**Rules:**
- **Same crypt for everyone, every UTC day.** 10 rooms in a straight line, reset at 00:00 UTC. The route is linear on purpose: with a shared seed, a branching map would reward scouting instead of play.
- **Doors** open only when the room is empty. Rooms scale from 6 to 14 Friends, with elites from room 5 and two families mixed from room 7.
- **After room 5**, choose 1 of 3 power-ups. Everyone gets the same three, and the clock stops while you choose.
- **Room 10 is the boss:** a real Friend at 6× size with four attacks, faster below half health. The clock stops when it falls.
- **No healing.** Each hit adds **+5 s**. You have **6 guard** (7 for Colossus), and the last hit ends the attempt.
- **Score** = clear time + 5 s × hits. Your Friend's family perk applies.

## Economy (simulated)

**All RF, balances, entries, the pool, the burn and rival times are simulated and labelled SIMULATED in the UI.** No contract is deployed and no RF moves.

| | |
| --- | --- |
| Ranked entry | **10 RF** per attempt, unlimited attempts |
| Burned | **20%** of every entry (2 RF) |
| Day pool | **80%** of every entry (8 RF) |
| Payout at 00:00 UTC | **50% / 30% / 20%** to the three fastest replay-verified times |
| Practice | Free, same crypt, never ranked |
| Starting balance | 100 RF, simulated, per session |

- **Spending:** a dead or forfeited attempt still pays its entry, and there's no refund. The game never mints RF: every payout comes from entries.
- **Burn at scale:** 200 attempts a day burn 400 RF a day, about 146,000 RF a year.
- **Edge cases:** unpaid places roll over to the next day, and an optional sponsored floor handles the cold start. See ECONOMY.md.
- **Chance-game API:** the SDK's chance game is not used, because an entry is a fixed fee, not a chance purchase. The required `game.json` carries **unused schema-only terms**: a 1 RF token with a single 10,000 bps reward of 1 RF, both `1000000000000000000` base units.

**Anti-cheat by replay:**
- The engine runs at a fixed 60 Hz step, with gameplay randomness taken only from the day's seed.
- Each tick's input is recorded (4 bytes) along with the power-up choice.
- A ranked run is **re-simulated from its inputs** and only the recomputed time is ranked. **Watch replay** plays the log back.
- In production this check runs on a server before payouts.

**Going live** needs custom integration beyond v0.1.2, which has no leaderboard, persistence or pool API:
- a pool contract (`enter` burns 2 RF from the Friend's canonical wallet and adds 8 RF to the day's pool);
- a verifier that settles the top 3 at the end of each day;
- a legal review of paid-entry prize contests.

## Checks, credits and limitations

- **Passed:**
  - `npx friendsdk check games/daily-crypt`: valid.
  - Strict `tsc -p games/daily-crypt/tsconfig.json`.
  - `npm test` (SDK): 114 passed, 2 skipped. `npm run typecheck` passes.
- **Headless bot simulation** (`node games/daily-crypt/tests/run-sim.mjs`), 54 full runs across all nine player families:
  - every honest run re-verifies by replay (27/27);
  - invulnerable, the bot clears the crypt in 22 of 27 runs, in roughly 2.5–6 minutes;
  - with normal guard it dies around room 4. It doesn't dodge, and the crypt is intended to be hard.
- **Browser check** (`node games/daily-crypt/tests/browser.mjs`): the real SDK runtime in headless Chromium with the SDK's mock wallet and RPC fixtures, extended for the artwork registry. It covers desktop and phone layouts, the ranked-entry confirmation and play, with no browser errors.
- **Real-wallet playtest:** done by the builder with a hardwired Generation 6 Friend.
- **Known check failure:** the stock `npx friendsdk test` fixture only answers artwork reads for sample Friend #7730, so it rejects the roster reads by design.
- **Limitations:**
  - Replays are bit-exact within one JavaScript engine. `Math.sin/cos/atan2/hypot` may differ between engines, so production verification needs fixed-point math.
  - The cast depends on the public Robinhood RPC, and a failed read shows Retry.
  - Family perks are not perfectly balanced; the leaderboard shows the family.
  - The sandbox has no storage, so balances and times reset on reload.
- **Credits:** code, rooms and sound effects by Fablizio (AI-assisted). Scenery is drawn in code. Character art: canonical Rare Friends Generations sprites via the FriendSDK sprite reader. Reward cues come from the FriendSDK sound kit (see the SDK `NOTICE.md`). The engine is shared with the builder's Character Spotlight entry, *The Binding of RareFriend* (#84). No trading, wearable NFTs, creator fees or live economy. Production publication needs separate Rare Friends review.
