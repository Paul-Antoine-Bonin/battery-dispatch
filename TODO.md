# battery-dispatch: trading a battery on the Portuguese day-ahead market

## Goal
Run a grid battery as a trader would: buy power in cheap hours, sell it in expensive
ones, under power, capacity, efficiency and wear limits. Measure how much of the
perfect-hindsight profit a realistic strategy captures when it has to commit before
prices are known.

## Data
OMIE day-ahead marginal prices for Portugal, in EUR/MWh. One public text file per day,
no account or API key. Hourly until 30 September 2025, 15-minute periods from
1 October 2025.

## Steps
- [ ] Set up repo, `src/` layout plus a demonstration notebook
- [ ] Collector for OMIE Portugal day-ahead prices from 2023, cached as CSV
- [ ] Clean to one regular time index: hourly and 15-minute periods, DST days with 23
      or 25 hours
- [ ] Battery model and perfect-foresight dispatch as a linear program (cvxpy): power
      and energy limits, charge and discharge efficiency, a wear cost per MWh cycled
- [ ] Rule-based baseline: each day, charge in the cheapest hours and discharge in the
      most expensive ones
- [ ] Price forecasts for the next day: naive ones first (yesterday, same day last
      week), then a simple regression
- [ ] Model predictive control: each day before gate closure, optimise over a 48 h
      forecast, commit only the next day, settle at the real prices
- [ ] Compare profit, cycles and share of the perfect-foresight profit captured;
      sensitivity to the wear cost and to forecast error
- [ ] README: the charts that tell the story

## Done when
A notebook that runs the three strategies over the same period and shows how much of
the theoretical profit forecasting errors cost, with the key charts in the README.

## Traps
- Perfect foresight is a ceiling, not a strategy. Never report it as a result on its
  own.
- The schedule for day D is fixed before day D prices are published. Any forecast or
  decision that reads a price it could not have known is a leak.
- Without a wear cost the optimiser cycles on every tiny spread. Keep the cost explicit
  and show how the result moves with it.
- Simultaneous charging and discharging can look profitable in an LP when prices are
  negative. Check that it never happens, or forbid it.
- Hourly and 15-minute periods must not be mixed in one series without converting MWh
  per period correctly.
- The battery is a price taker here: its own bids do not move the price. Say so in the
  README.
