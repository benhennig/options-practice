# Black-Scholes Option Pricer (practice)

MSF 8615 Derivatives, Villanova University · Team: Ben Hennig (individual practice repository)

**Live app:** not deployed yet. Run it locally with `streamlit run app.py` (opens at http://localhost:8501).

## The question
What is a European call and put on a non-dividend-paying stock worth under Black-Scholes-Merton, and do
the two prices satisfy put-call parity?

## Inputs and outputs
- **Inputs:** underlying price S, strike K, time to expiry T (years), risk-free rate r (% per year,
continuously compounded) and volatility sigma (% per year), all typed in by the user.
- **Outputs:** the Black-Scholes call price c and put price p, plus a put-call parity check showing
c − p next to S − K·e^(−rT). The two numbers should match; if they do not, something is wrong in the code.

## Data
The app uses no data files. Every input is typed in by the user, and the defaults (S = 42, K = 40,
r = 10%, sigma = 20%, T = 0.5) are the hypothetical worked example from Hull, not observed market prices.

| File in `data/` | Source | Date and time saved | Terms of use: can it be public? |
|---|---|---|---|
| (none used) | n/a | n/a | n/a |

- Save market data as dated files, for example `cboe_spx-options_2026-10-20.csv`, and show the date
on screen.
- Licensed data (Bloomberg, FactSet, Risk.net, WRDS, ...) does not go into a public repository or
into AI tools. Keep it in `data/licensed/` (ignored by git) and run your code on it yourself.
- Hypothetical inputs and stress scenarios are fine. Label them as such.

## Method
Black-Scholes-Merton for European options on a stock that pays no dividends (Hull, *Options, Futures,
and Other Derivatives*, 11th ed., Ch. 15):

- d1 = [ln(S/K) + (r + sigma²/2)·T] / (sigma·√T), and d2 = d1 − sigma·√T
- Call: c = S·N(d1) − K·e^(−rT)·N(d2)
- Put: p = K·e^(−rT)·N(−d2) − S·N(−d1)

N(·) is the standard normal cumulative distribution function, computed with the error function in
`pricing.py`. The model assumes the stock price follows geometric Brownian motion with constant volatility
and a constant risk-free rate, no dividends, no transaction costs, and exercise only at expiry.

## Tests
Run `pytest`. Each test compares the code with a number checked independently:
- `tests/test_pricing.py`: Black-Scholes prices match Hull's Chapter 15 example; put-call parity holds.
  - Call = 4.76 and put = 0.81 for S = 42, K = 40, r = 10%, sigma = 20%, T = 0.5 (Hull, Ch. 15).
  - c − p equals S − K·e^(−rT) to within 1e-9.
  - A negative volatility raises an error instead of returning a price.

The agent skill in `.agents/skills/check-the-numbers/` tells coding agents how to add checks like these.

## How to run
In a GitHub Codespace, or in VS Code on your laptop (first follow
[`docs/local-setup.md`](docs/local-setup.md); this also works where Codespaces are blocked):
```
pip install -r requirements.txt
pytest
streamlit run app.py
```
New to Streamlit? [`docs/streamlit.md`](docs/streamlit.md) explains how an app works (the script
reruns from top to bottom on every click), with caching, layout and deployment.

## Limitations
- European exercise only: it does not price American options, where early exercise can matter (for
example, American puts).
- No dividends: the prices are wrong for stocks or indexes that pay dividends before expiry.
- Volatility and the risk-free rate are user assumptions, held constant; real implied volatility varies by
strike and maturity (the smile), so a single sigma will not reproduce market prices across strikes.
- All inputs are scenarios typed by the user, not observed prices.

## AI use
Setup and this README were drafted with help from Claude (Anthropic). No AI wrote pricing code so far.
Details in `AI_LOG.md`.

## Team contributions
| Name | GitHub username | Main contributions |
|---|---|---|
| Ben Hennig | benhennig | Repository setup, README |

---

## Project objective: explain and verify
The team can explain what the app computes, why the method fits the question, and how each
displayed number was verified, and every member presents part of the group demo. The team is
responsible for the output, whoever or whatever wrote the code. The evidence: tests against
independent numbers (`tests/`) and `AI_LOG.md` (AI use, the critic's review, examiner practice),
scored against [`RUBRIC.md`](RUBRIC.md). Every member also makes at least one commit under their
own GitHub account (criterion 7).

## Files in this repository
| File | Who uses it | What it is for |
|---|---|---|
| `README.md` | You | This page: the question, data, method, checks, limitations and team |
| `AI_LOG.md` | You | How you used AI, the critic's findings and your fixes, and examiner practice |
| `RUBRIC.md` | You read it | How the app is graded |
| `AGENTS.md` | The agent | Standing rules it reads every session. Add a rule when it repeats a mistake |
| `PLAN.md` | The agent | Notes on the current task, so a new session can pick up where the last one stopped |
| `app.py`, `pricing.py`, `tests/`, `data/` | Code | The page, the formulas, the checks, the dated data files |
| `docs/` | You, when needed | Guides: [laptop setup](docs/local-setup.md), [coding agents](docs/agent-setup.md), [Streamlit](docs/streamlit.md), [git as a team](docs/git-and-teamwork.md) |

To read a `.md` file with its formatting: on github.com, click it. In a Codespace, right-click it
and choose **Open Preview** (Ctrl+Shift+V).

## Working with agents
Set up the agent you want with [`docs/agent-setup.md`](docs/agent-setup.md): GitHub Copilot (free
for students), Antigravity CLI, Codex or Claude Code. All of them read `AGENTS.md`. Use each tool's
built-in plan mode to plan a step. Two review helpers live in `.github/agents/`:
- `critic`: adversarial review in a new session; cannot edit files; findings go in `AI_LOG.md`
- `examiner`: asks practice questions for the group demo and scores them against `RUBRIC.md`

In Copilot, pick them from the agent menu. In other tools, start a new session and ask the agent
to read the file and follow it (`docs/agent-setup.md` has the prompt).

## Working as a team
See [`docs/git-and-teamwork.md`](docs/git-and-teamwork.md): pull before you start, one owner per
file, push when a piece of work is done, and what to do when git reports a merge conflict.
