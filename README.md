### Alessandro Moscardo
Software Engineer based in Doorn, the Netherlands · EU citizen

For 16 months I built software for railway systems at Alstom (via ALTEN).
That code is private, so here is what it covered:

- **Data pipelines** — Python/Dagster pipelines turning daily 2 kHz train telemetry into
  diagnostic reports and ML-ready datasets, with automated data-quality checks and alerts.
- **C#/.NET** — refactored a WPF diagnostic tool into DI services and ViewModels; it now
  supports 5 train fleets and 30,000+ signals through XML configuration.
- **Configuration tooling** — Python tool that reads 140+ Excel interface workbooks, checks
  them for duplicates and bit-offset conflicts, and generates the train-network XML
  configuration, validated against the client's XSD schema.

Lately I've also been building LLM applications with retrieval, tool calling and tests.

**Selected projects**
- **[Trainer Red](https://github.com/alemoscardo/alemoscardo/tree/main/trainer-red)** *(technical write-up; code private)* —
  co-developed a self-play AI for competitive Pokémon VGC Doubles that reached **#2 worldwide**
  on the official Showdown ladder (peak Elo 1784): battle engines in JAX and Rust, a Transformer
  policy trained with PPO on 8 GPUs, and CFR search at play time. Now public as
  **[wispcoach.com](https://wispcoach.com)**.
- **[SignalWatch AI](https://github.com/alemoscardo/SignalWatch-AI)** — RAG agent that investigates
  telemetry alerts through read-only tools over PostgreSQL/pgvector, with citation-backed reports.
- **[Premier League Outcome Model](https://github.com/alemoscardo/ml-football-predictions)**
  ([live demo](https://alemoscardo-ml-football-predictions-streamlit-app-vkbxgd.streamlit.app/)) —
  forecasts match results from pre-match data only (Elo, recent form, rest days), validated
  chronologically; on an unseen season it lands within 0.01 log-loss of Bet365.

**Stack:** Python · C#/.NET · SQL · Dagster · PostgreSQL · Docker · GitHub Actions · PyTorch · JAX · scikit-learn

[LinkedIn](https://www.linkedin.com/in/alessandro-moscardo/)
