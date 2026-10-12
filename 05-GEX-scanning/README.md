# 05 - GEX Scanning: live gamma exposure, American options, Numba CUDA

Code for the QuantPy video [Gamma Exposure and Market Volatility](https://youtu.be/2TC2xNPfLdk).

| Notebook | In the video | What it is |
|---|---|---|
| [gex_dealer_hedge_example.ipynb](gex_dealer_hedge_example.ipynb) | 3:22 | One SPY put and one SPY call at two closes: spot, premium, delta, gamma, and the dealer's hedge. Runs from the four data files in `data/`, no API key needed. |
| [intraday_American_IV_GEX_Dash_Only.ipynb](intraday_American_IV_GEX_Dash_Only.ipynb) | 6:41 | The live GEX dashboard: the 100 most options-traded US names, open interest once a day, quotes every update, American implied vol and gamma on the GPU, alerts and a gamma-flip table. |
| [intraday_American_IV_GEX.ipynb](intraday_American_IV_GEX.ipynb) | 8:23 | How it is built: Theta Data pull and cache, QuantLib reference, Numba CPU and Numba CUDA engines, validation, timing, then the live session. |

## Measured

One-minute snapshot of 99 tickers, 85,814 options: implied vol solved on a 200-step Cox-Ross-Rubinstein binomial tree,
gamma over 81 spot prices (69.4 M gamma evaluations per 10 minutes). NVIDIA RTX PRO 6000 Blackwell (96GB),
AMD Threadripper PRO 7965WX. From `intraday_American_IV_GEX.ipynb`.

| engine | one minute | against QuantLib |
|---|---|---|
| QuantLib, Python wrapper (extrapolated) | 1,111 s | 1x |
| Numba, CPU | 5.3 s | 215x |
| Numba CUDA | 0.69 s | 1,555x |

Validation against QuantLib on the same snapshot: median implied-vol difference 1e-5, SPY GEX at spot within 0.002 bn $ (table in the notebook).

## Run it

```
cd 05-GEX-scanning
uv sync
cp .env.example .env            # THETADATA_API_KEY=...
uv run jupyter lab intraday_American_IV_GEX_Dash_Only.ipynb
```

Requirements: NVIDIA GPU (CUDA 13 wheels through `numba-cuda[cu13]`), a [Theta Data](https://www.thetadata.net) subscription for the
two live notebooks ([Python API docs](https://docs.thetadata.us/Python-Library/Getting-Started.html)). Dividend yields come from yfinance.
Both live notebooks call `cuda.select_device(1)` (this box has two GPUs); on a single-GPU machine set it to 0.

GEX = gamma x open interest x 100 x spot^2 x 0.01, calls positive, puts negative. The sign assumes dealers are long the calls
and short the puts that customers trade; that is an assumption, not an observation. The 3-year SPY study behind the
regime claim is in [05-GEX-research](../05-GEX-research). Educational analysis, not a trading strategy, not financial advice.
