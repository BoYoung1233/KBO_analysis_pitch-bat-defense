# KBO Team Win Rate Analysis (Part 2): Integrating Pitching and Batting Data

*[한국어로 보기](README_KOR.md)*

A follow-up to [Part 1 — KBO Defensive Analysis](https://github.com/BoYoung1233/KBO_defense_analysis). Part 1 analyzed team win rate using defensive metrics alone, finding that defense explained only a limited share of the variance. This project integrates pitching and batting data to close that gap and asks which of defense, pitching, and batting matters most for winning.

- **Period analyzed**: 2001–2021 (intersection of pitching, batting, and defensive data)
- **Unit of analysis**: team-season (184 observations, 11 unified franchises)

## Data

| Item | Source |
|---|---|
| Defensive records (2001–2024) | Part 1 output, based on Kaggle |
| Pitching records (1982–2021) | Kaggle, `kbopitchingdata.csv` |
| Batting records (1982–2021) | Kaggle, `kbobattingdata.csv` |
| Regular-season win rate | KBO official website (cross-validation) |

## Methodology

- Unified franchise names across historical rebrands (e.g., SK Wyverns/SSG Landers → Incheon)
- Merged and cross-validated the three datasets (`outer` join + `indicator` check, then `inner` join); resolved a win-rate discrepancy traced to a games-count error in the pitching data
- Screened candidate metrics for multicollinearity within each category (defense/pitching/batting) and selected representative indicators
- Standardized (z-score) the selected metrics and ran a multiple regression against win rate
- Supplementary analyses: DIPS theory (FIP-ERA gap), Pythagorean win expectancy residuals, and a scoring power vs. run prevention index comparison

## Key Findings

**1. Multiple regression** — ERA, K/BB, OPS, and putouts_per_game together explain **84.5%** of win rate variance (R²=0.845), a large improvement over Part 1's defense-only model (R²≈0.31). ERA and OPS were the strongest, most significant predictors.

![Standardized coefficients](image2/no.1.png)
![Actual vs. predicted win rate](image2/no.2.png)

**2. DIPS theory (FIP-ERA gap)** — The largest gap belonged to the 2014 NC Dinos (+0.791) and the smallest to the 2013 Kia Tigers (-0.788). Comparing these against putouts_per_game showed almost no difference between the two extreme cases (26.6 vs. 26.8). This is likely because putouts_per_game is largely fixed by game rules and also includes strikeouts — a pitching, not defensive, skill. BABIP proved more suitable: larger gaps corresponded to lower BABIP and smaller gaps to higher BABIP (r=-0.48, stronger than putouts_per_game's r=0.40).

**3. Pythagorean residual analysis** — Residuals varied season to season even within the same team (e.g., Hanwha Eagles, a historically weak franchise, ranked #1 in average residual), suggesting they reflect season-specific factors rather than fixed team strength. Residuals showed a weak link to saves (r=0.29) but essentially none to the FIP-ERA gap (r=-0.15).

**4. Scoring power vs. run prevention** — A two-index model reached R²=0.726. After correcting for variance compression from averaging z-scores, run prevention proved roughly **1.58x stronger** than scoring power in driving win rate.

## Limitations

- Save data is a raw count and lacks save-opportunity/game-level detail, limiting close-game analysis
- Pitching/batting data only extends through 2021 (2022–2024 excluded)
- wOBA was replaced with OPS due to the absence of KBO-specific linear weights
- putouts_per_game and ERA show a weak correlation (-0.47), so their contributions may partially overlap
- The FIP constant is approximated from league averages rather than an official KBO coefficient

See the notebook for the full discussion and future work.

## Tech Stack

Python (pandas, statsmodels, scipy.stats, matplotlib, seaborn), Jupyter Notebook

## Repository Structure

```
├── KBO2.ipynb
├── README2.md
├── README_KOR2.md
├── image2/
└── data/
```

## Related

- [Part 1 — KBO Defensive Analysis](https://github.com/BoYoung1233/KBO_defense_analysis)
