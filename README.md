# Equity Index Construction & Backtesting Engine

Un moteur complet en Python pour construire, rééquilibrer et backtester un indice actions (Equity Index), de l'acquisition des données à l'évaluation de la performance risque-rendement.

## Objectif du projet
Construire un indice **Equal-Weight** sur un univers de grandes capitalisations US, avec une contrainte de diversification sectorielle, et évaluer sa performance contre un benchmark (S&P 500 - SPY) sur la période 2018-2023.

## Pipeline (6 étapes)
Le projet est architecturé en modules indépendants, orchestrés par `main.py` :
1. **Data Acquisition** (`data_loader.py`) : Récupération des prix ajustés et des fondamentaux via `yfinance`.
2. **Stock Screening** (`screener.py`) : Filtrage de l'univers (Market Cap > $2B, Volume > $5M, Prix > $5, secteurs autorisés, exclusion REITs).
3. **Index Construction** (`index_constructor.py`) : Sélection du Top 50 par capitalisation, pondération Equal-Weight, et application d'un **cap sectoriel de 30%** (algorithme itératif de redistribution).
4. **Portfolio Rebalancing** (`backtester.py`) : Rééquilibrage calendaire trimestriel (fins de trimestre). Calcul du turnover et application des coûts de transaction (10 bps).
5. **Backtesting** (`backtester.py`) : Simulation de la NAV jour par jour, avec maintien des poids cibles entre deux rééquilibrages.
6. **Risk-Performance Evaluation** (`metrics.py`) : Calcul des métriques standards (Sharpe, Sortino, Drawdown, Alpha, Beta, Information Ratio).

## Résultats du Backtest (2018 - 2023)

| Métrique | Indice Custom | Benchmark (SPY) |
| :--- | :--- | :--- |
| **Rendement Annualisé** | **15.43%** | 11.86% |
| **Volatilité Annualisée** | 24.41% | - |
| **Sharpe Ratio** | 0.55 | - |
| **Max Drawdown** | -38.34% | ~ -34% |
| **Alpha (Jensen)** | **2.73%** | - |
| **Beta** | 1.108 | 1.0 |
| **Information Ratio** | 0.427 | - |

### Interprétation :
- **Surperformance** : L'indice génère un alpha positif de 2.73% et un rendement annualisé de 15.43% (vs 11.86% pour le S&P 500).
- **Profil de risque** : La volatilité (24.4%) et le drawdown (-38%) sont plus élevés que le marché. C'est le coût de l'Equal-Weight, qui surpondère les mid-caps (plus volatiles).
- **Concentration temporelle** : La surperformance est portée par les années 2019 et 2023 (reprise des mid-caps). L'indice sous-performe en 2021 (domination des mega-caps type Magnificent 7).

## Limites assumées (Points de transparence)
Pour un projet de production, les améliorations suivantes seraient nécessaires :
1. **Look-ahead & Survivorship Bias** : L'univers est codé en dur et le screening n'est pas recalculé point-in-time. Les données `yfinance` sont actuelles, pas historiques.
2. **Alpha mono-facteur** : L'alpha de Jensen est basé sur le CAPM. Une partie de la surperformance pourrait être expliquée par un biais de taille (Small/Mid-cap premium). Un modèle Fama-French serait plus rigoureux.
3. **Taux sans risque constant** : Fixé à 4% dans `config.py` pour simplifier, alors que les taux réels ont varié de 0% (2020) à 5% (2023).
4. **Coûts de transaction linéaires** : Modélisés à 10 bps, sans prendre en compte le market impact ou les coûts d'entrée initiaux.

## Comment exécuter
```bash
pip install -r requirements.txt
python main.py
