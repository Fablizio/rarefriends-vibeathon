## Friend Guild

A guild-management game where Rare Friends work for each other. Hire real Friends as mercenaries: 70% of every fee goes to the hired Friend's own wallet, 20% is burned and 10% funds the season.

**Builder:** Fablizio · [GitHub @Fablizio](https://github.com/Fablizio) · [X @FabrizioCottone](https://x.com/FabrizioCottone) · [Telegram @Fablizio](https://t.me/Fablizio) · **Category:** Economy Potential · **SDK:** FriendSDK v0.1.2

Every hardwired Generations Friend has its own canonical wallet, and Friend Guild gives that wallet a job. Your verified Friend runs a guild and hires real Friends (read live from the SDK's artwork registry) for expeditions, and each fee pays the hired Friend's owner. Expeditions never create RF: they bring Shards, a soft currency that is spent together with RF (burned) on upgrades and gear. An in-game economy simulator runs the whole economy for 30 days with adjustable parameters.

- **Play:** https://fablizio.github.io/friend-guild/
- **Source:** https://github.com/Fablizio/friend-guild/tree/3155a7e573a38975ae1d9c4121748ce296767ac9 (game in [`games/friend-guild/`](https://github.com/Fablizio/friend-guild/tree/3155a7e573a38975ae1d9c4121748ce296767ac9/games/friend-guild))
- **Economy design:** [`ECONOMY.md`](https://github.com/Fablizio/friend-guild/blob/3155a7e573a38975ae1d9c4121748ce296767ac9/games/friend-guild/ECONOMY.md)
- **Wallet and network:** a browser wallet on **Robinhood mainnet (4663)** holding a hardwired Generations NFT (generation ≥ 1). On a phone, open the link in your wallet's in-app browser. The SDK runtime handles connection, Friend selection and the fresh ownership check. No transaction or signature is requested.

## Run it

Node.js 22+ on Linux or Ubuntu/WSL2:

```sh
git clone https://github.com/Fablizio/friend-guild.git
cd friend-guild
git checkout 3155a7e573a38975ae1d9c4121748ce296767ac9
npm ci
npm run build
npm run dev:game -- games/friend-guild
```

Open `http://localhost:4173`, connect your wallet and select your Friend. Static build: `npx friendsdk build games/friend-guild`.

## Play

Tap or click. Everything is also keyboard-accessible.

- **Guild:** your Friend's family and seed stats, rating, hire fee and gear.
  - **List** it in the tavern and simulated guilds hire it, with 70% of each fee going to its wallet.
  - A live feed shows every hire, and the season board ranks guilds by fame.
- **Tavern:** eight real Friends with stats, trait and live fee.
  - Hire up to 2 for your next expedition. Each hire raises that Friend's price by 5%, and demand fades over time.
- **Expedition:** four family zones (tier 1–4). Each zone favours two counter-families.
  - The success chance (team power, guild level, affinity) is shown before launch.
  - A short animated run has four encounters against real Friends (12–24 s demo speed, skippable).
  - Rewards: Shards, fame and sometimes gear. **Never RF.**
- **Workshop:** guild upgrades (+3% success per level, up to 5) and gear crafting (+2 to a stat). Both cost Shards plus RF, and the **RF is 100% burned**.
- **Economy:** a 30-day agent-based simulator with charts, where you can change players, expeditions per day, burn %, owner % and price step.
- **Settings:** Mute and Reduce motion.

## Economy (simulated)

**All RF, balances, hires, other guilds, fame and earnings are simulated and labelled in the UI.** No contract is deployed and no RF moves.

| | |
| --- | --- |
| Mercenary fee | (1 + 0.22 × rating) RF × 1.05^recent hires |
| Fee split | **70% hired Friend's canonical wallet · 20% burned · 10% season fund** |
| Guild upgrade | ◆ 40·L + 8·L RF (RF burned) |
| Gear craft | ◆ 30 + 4 RF (RF burned) |
| Expeditions | Shards, fame, gear. **No RF** |
| Season fund | Weekly 50/30/20 to the top guilds by fame |
| Starting balance | 150 RF, simulated, per session |

- **Backing:** the game mints no RF. Shards and gear are never redeemable for RF, so no prize backing is required.
- **Simulator, default settings** (1,000 players, 3 expeditions a day, 30 days):
  - about 2.25M RF spent: ≈720K burned and ≈1.34M paid to Friend owners;
  - the average fee settles from 7.7 to 10.9 RF;
  - the top 10% of Friends earn about 15% of owner income;
  - Shard supply levels off;
  - the conservation check passes.
- **Chance-game API:** the SDK's chance game is not used. The required `game.json` carries **unused schema-only terms**: a 1 RF token with a single 10,000 bps reward of 1 RF, both `1000000000000000000` base units.
- **Going live** needs custom integration beyond v0.1.2, which has no hire, listing, currency or persistence API:
  - a GuildHire contract (`hire` pays 70% to the canonical wallet, burns 20% and sends 10% to the season);
  - a game server for Shards and fame;
  - a legal review of holder earnings.

## Checks, credits and limitations

- **Passed:**
  - `npx friendsdk check games/friend-guild`: valid.
  - Strict `tsc -p games/friend-guild/tsconfig.json`.
  - `npm test` (SDK): 114 passed, 2 skipped. `npm run typecheck` passes.
- **Model checks** (`node games/friend-guild/tests/run-econ.mjs`): 9 passed. Fee splits conserve RF, the model never mints RF and is deterministic, a higher burn burns more, earnings stay spread, and Shard supply is bounded.
- **Browser check** (`node games/friend-guild/tests/browser.mjs`): the real SDK runtime in headless Chromium with the SDK's mock wallet and RPC fixtures, extended for the artwork registry. It walks guild → hire two → launch → skip → result → workshop → economy on desktop and phone layouts, with no browser errors.
- **Real-wallet playtest:** done by the builder with a hardwired Generation 6 Friend.
- **Known check failure:** the stock `npx friendsdk test` fixture only answers artwork reads for sample Friend #7730, so it rejects the tavern's roster reads by design.
- **Limitations:**
  - Other guilds and their hires are simulated in the browser, and hires are non-exclusive.
  - No persistence: the session resets on reload.
  - The simulator is a model with stated assumptions, not a forecast.
  - Tavern Friends depend on the public Robinhood RPC, and a failed read shows Retry.
- **Credits:** code and design by Fablizio (AI-assisted). Scenery is drawn in code. Character art: canonical Rare Friends Generations sprites via the FriendSDK sprite reader. Sounds come from the FriendSDK sound kit (see the SDK `NOTICE.md`). The same builder's other entries are *The Binding of RareFriend* (#84, Character Spotlight) and *Daily Crypt* (#85, Token Activity). No trading, wearable NFTs, creator fees or live economy. Production publication needs separate Rare Friends review.
