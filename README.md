<div align="center">

# strategy-search

**Evolutionary ML search over 1M+ trading strategy configurations**

[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![License](https://img.shields.io/badge/license-MIT-94A3B8?style=flat-square)](.)

*Discovers optimal EUR/USD trading strategies by training thousands of transformer models and evolving the best performers over 200 generations.*

</div>

<br>

## How It Works

```
Random population (16 strategies)
        |
        v
Train each on EUR/USD data
        |
        v
Backtest on 5 held-out weeks across 5 years   <- prevents overfitting
        |
        v
Score by composite fitness: Sharpe + Win Rate + (1 - Max Drawdown)
        |
        v
Evolve: keep top 25%, crossover 50%, mutate 30%, random explore 25%
        |
        v
Repeat for 200 generations (~3,200 strategies total)
```

The 5 validation weeks span different market regimes: Covid crash (Mar 2020), Ukraine war (Feb 2022), summer chop (Jul 2021), fall volatility (Oct 2023), recent data (Jun 2024).

<br>

## Quick Start

```bash
git clone https://github.com/AshtonVaughan/strategy-search.git
cd strategy-search
pip install -r requirements.txt
python main.py
```

Edit `config.yaml` to adjust the search space, fitness weights, or validation weeks before running.

<br>

## Search Space

| Category         | Parameters                                          |
|------------------|-----------------------------------------------------|
| Model            | Hidden size, layers, attention heads, sequence length |
| Training         | Learning rate, batch size, epochs, weight decay     |
| Strategy         | Confidence threshold, SL/TP pips, position sizing   |
| Total configs    | ~500,000+                                           |

<br>

## Fitness Function

```
Fitness = 0.4 x Sharpe + 0.3 x WinRate + 0.3 x (1 - MaxDrawdown)
```

Weights are configurable in `config.yaml`.

<br>

## Expected Results

After 200 generations on an RTX 5090 (~17-25 days total):

| Metric       | Expected range |
|--------------|----------------|
| Win rate     | 70-75%         |
| Sharpe ratio | 2.0-2.5        |
| Max drawdown | <15%           |
| Fitness      | 0.80-0.90      |

Reduce generations to 100 and epochs to 20 to cut runtime roughly in half.

<br>

## Project Structure

```
main.py                  Main orchestrator
config.yaml              All configuration
search/evolution.py      Evolutionary algorithm
training/model.py        Transformer architecture
training/trainer.py      Model training
backtesting/engine.py    Vectorbt backtesting
validation/              5-week cross-validator
outputs/checkpoints/     Saved progress (every 10 generations)
outputs/reports/         Final results JSON
```

<br>

## Output

Checkpoints are saved every 10 generations. The final report at `outputs/reports/search_results.json` contains the top 50 strategies with full configs and metrics.

<br>

## Disclaimer

For educational and research purposes only. Past backtest performance does not guarantee future results. Not financial advice.

<br>

## License

MIT
