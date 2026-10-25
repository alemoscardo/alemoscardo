# Trainer Red

**An AI that taught itself competitive Pokémon doubles and reached #2 on the global ladder.**

<table>
<tr>
<td width="50%" align="center"><img src="assets/ladder-rank2.png" width="100%" alt="Official Pokémon Showdown ladder for Reg M-C best of three, 14 October 2026: CrusaderBigDam, the Trainer Red bot, at rank 2 with Elo 1768."><br><sub><b>#2 worldwide</b>, 14 October 2026 · Elo 1768</sub></td>
<td width="50%" align="center"><img src="assets/ladder-peak-elo.png" width="100%" alt="Official Pokémon Showdown ladder, 16 October 2026: CrusaderBigDam at rank 3 with its peak Elo of 1784."><br><sub><b>Peak Elo 1784</b>, 16 October 2026 · #3</sub></td>
</tr>
</table>
<p align="center"><sub>Official Showdown ladder, Champions VGC Reg M-C best of three. The bot plays as <i>CrusaderBigDam</i>.</sub></p>

<p align="center"><img src="assets/timeline.svg" width="100%" alt="Timeline: June 2026 first agent; September parity-first JAX engines never compile; 30 September vgcjax passes every parity gate; 5–6 October 127.6M games in 15 hours on 8 GPUs; 14 October rank 2 worldwide."></p>

VGC doubles is a hard game for an AI. Both players move at the same time, each turn offers up to 729 joint actions per side, the opponent's stats are hidden and every hit rolls dice. This is how a two-person side project went from zero to #2 on the global ladder in four months. *The source code is private; this write-up covers the ideas and the results.*

---

### 1. Making the game fast

Learning by self-play takes hundreds of millions of games, and the reference simulator, Pokémon Showdown, runs one battle at a time. The first attempt ported it to JAX event by event. It needed over a thousand nested branches and **never finished compiling**, even with 128 GB of RAM.

So the engine, **vgcjax**, was rewritten from scratch around what GPUs are good at. There is no branching in the game logic: every rule is masked array arithmetic, so thousands of battles advance in lockstep. Moves, abilities and items are rows of data, not code paths. A compile budget in CI rejects any change that makes the program too large. The result compiles in **27 seconds** and runs **7.55 million games per hour on a single consumer GPU**.

<p align="center"><img src="assets/architecture.svg" width="100%" alt="Rules exported from Showdown feed the vgcjax engine, which drives self-play training; the trained policy and the same engine power the decision-time search behind the web app and the ladder bot."></p>

### 2. Making it correct

Speed is useless if the simulator plays a different game. vgcjax does not copy Showdown's random sequence, so it is checked statistically instead. Across 50 team pairings with 2,000 games each on both simulators, **every win rate lands within ±3 points** of Showdown's. **319,990 of 320,000 damage rolls** match exactly. A 10,000-game fuzz found zero invalid states. The thresholds were fixed before the tests and never moved.

Later the project went one step further: a **second engine in Rust**, a function-by-function port of Showdown that reproduces it **identically on 10,000 battles**, logs and random draws included. It is now the default engine for both training and search: training runs **26% faster** on the same GPU and the play-time search **2.4× faster**.

<p align="center"><img src="assets/parity-chart.svg" width="100%" alt="All 50 team pairings within plus or minus 3 percentage points of Showdown's win rate."></p>

### 3. Teaching it to play

A **~7M-parameter Transformer** reads the board as 61 tokens (the field, twelve Pokémon and their moves) and keeps a recurrent memory of what the game has revealed. It sees exactly what a human player would see. Its action head scores all 729 joint actions together, so the two Pokémon on the field learn to coordinate. It was trained with **PPO through self-play on 8× RTX 5090**: 127.6 million games in a single 15-hour run, and **more than 450 million** across all training generations.

<p align="center"><img src="assets/policy-network.svg" width="100%" alt="61 tokens through a Transformer encoder with recurrent memory; heads for joint actions, team preview and value."></p>

### 4. Thinking at play time

On the ladder the network does not play alone. At every decision it shortlists promising actions for both players. The engine simulates each pairing, averaging over dice and over the opponent's hidden stats, and the network's value head scores the outcomes. The turn is then solved as a matrix game with **counterfactual regret minimisation (CFR)**. The move is drawn from the resulting mixed strategy, so the bot is hard to predict.

### 5. Putting it on the ladder

A Node.js client turns Showdown's live battle protocol into the engine's view of the game. It plays ranked best-of-three series against humans, logs every decision so it can be replayed, and stops on any fault. On 14 October 2026 the account reached **#2 worldwide**, and two days later it set its **peak Elo of 1784**.

### 6. Opening it to players

The same AI now powers **[wispcoach.com](https://wispcoach.com)**, a public web app built with React and TypeScript. Players battle Wisp at the level they choose, solve puzzles and get their own games analysed.

---

**My role.** I led both engines (design, most mechanics, validation; the Rust port) and built the search and the ladder bot; the public site is a team effort. My collaborator built the first training pipeline; since October I have led the later training generations, the evaluation and the ladder campaigns.

<sub>JAX · XLA · Rust · Python · CUDA · multi-GPU training · Node.js · TypeScript · React · Docker — Pokémon is a trademark of Nintendo, Creatures Inc. and GAME FREAK inc.; independent project.</sub>

[← Back to my GitHub profile](https://github.com/alemoscardo)
