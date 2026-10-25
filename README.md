# MTF Liquidity Dealing Range

A TradingView indicator (Pine Script v6) that builds the dealing range from liquidity that has actually been taken, across several timeframes at once.

I built it from ICT's 2022 Mentorship dealing-range lecture. A dealing range only exists once liquidity has framed it: one side is purged, then the other. Until both have happened there is nothing to measure premium and discount against, and this script draws no range.

It tells you where price sits inside a range that liquidity has defined. It does not tell you which way to trade.

## What it draws

**Liquidity register.** Swing highs and lows from up to five timeframes, three sessions, and the previous day's high and low. Levels at the same price merge into one line with combined tags, so a level that is both 1H and 4H structure reads `1h,4h`. A level is swept by a wick, a touch counts, and its line stops at the candle that took it.

**Dealing range.** High, low and equilibrium, framed only by registered sweeps. The side being tracked follows price to its extreme.

**Premium / discount box.** Drawn over the range and split once at 0.5. Nothing else.

**Draw on liquidity.** After the last sweep, the nearest unswept pool on the opposite side is marked with its tags.

**Odds.** Every live level is priced as the chance of reaching it before a 1R stop with no edge at all. It is the number your read has to beat, not a forecast.

**Volume context.** Optional. A volume profile, point of control and value area anchored to the range. VAH and VAL bound the band around the POC that holds 70% of the volume traded inside the range: where the market accepted price, and where it didn't. By default each candle's volume is spread evenly from its high to its low. With "Real volume at price" on, it is placed at the prices it actually traded, from TradingView's footprint data. That needs Premium or Ultimate, so it is off by default; on a lower plan the script stops with an error until you switch it off. Candles with no footprint data fall back to the even spread, and the POC label's tooltip says how many used real data. On the CME clock, candles printed while CME was shut add no volume.

## Reading it

The last sweep sets the draw. If sell-side was taken last, the draw is the nearest buy-side pool, and the box shows whether price is in the premium or discount half of the range you set as primary.

Premium is not a short and discount is not a long. Bias comes from your own narrative. The range only tells you whether price is somewhere worth acting from once you have one.

If one candle takes both sides, the script does not guess which came first. It applies only what is true in either order and suspends the draw until the next one-sided sweep.

## The three ranges

| Range | What moves it | Default |
|---|---|---|
| Rotation | Any opposing registered sweep re-anchors that side | On |
| IPDA Frame | Only a sweep at or beyond a boundary, and only outward. Contracts on time: no boundary older than 20, 40 or 60 daily bars | Off |
| Delivery | A raid, then a candle close through the last opposing swing. Runs from the raid extreme to the displacement extreme | Off |

**Primary range** picks which one the box, equilibrium, draw, odds, volume profile, table and alerts measure against. If the one you picked is not live, it falls back to one that is and says so in the table.

**Manual range.** Sometimes I want the range I see, not the one liquidity framed. Pick "Manual" as the primary range and set two times, the same way as the Dated DR in Dealing Range Liquidity Reversal. Its high and low are the highest high and lowest low of the candles between them, anchored at the candles that made them. While the second time is still ahead it keeps growing; after that it is fixed, and price trading beyond it reads as above premium or below discount instead of moving it. Everything downstream measures against it. The automatic ranges keep running underneath and draw quieter, so switching back costs nothing. This breaks the rule the rest of the tool follows, that no range exists until liquidity frames one: it is your read, not the tool's.

## Settings

| Group | What is in it |
|---|---|
| 1. Timeframes | Five swing sources (1H, 4H and Daily on; Weekly and 15m off), levels kept per tag, swing length |
| 2. Sessions | Three sessions with editable names and times, New York time. Asia 20:00–00:00 and London 02:00–05:00 on, New York 09:30–12:00 off |
| 3. Daily levels | PDH / PDL from the completed daily bar. Daily reset. CME clock for perps |
| 4. Register style | Line style, swept-level history, origin labels, hide levels beyond a distance, merge tolerance |
| 5. Dealing range | The three ranges, primary selector with a manual range, labels, colours, premium / discount box |
| 6. Delivery range | Toggle and style |
| 7. Draw | Mark the draw |
| 8. Odds | Risk from an ATR multiple, manual points or the last swing. Round-trip cost. How far out to price |
| 9. Volume context | POC line, value area, real volume at price (Premium), profile, row height, lookback |
| 10. Table | Position and size |

Only sources at or above the chart timeframe are used. On a 1H chart that is 1H, 4H and Daily. On a 4H chart, 4H and Daily. A source finer than the chart is skipped, not approximated.

## CME clock

I trade perps that mirror CME markets, and a perp keeps printing when CME is shut. A weekend wick on XYZ100 that takes a 1H high never happened on NQ, and it should not frame a range. So on a trade.xyz market (HIP3XYZ) or Binance's CL perp, on an intraday chart, the script reads the perp as the futures contract would print it:

- candles from Friday 17:00 to Sunday 18:00 New York, and in the daily 17:00 to 18:00 halt, are ignored. They sweep no level, move no range, build no session and print no swing
- the day runs 18:00 to 17:00 New York, as on CME. The daily roll, PDH / PDL and the IPDA day count follow that day, and PDH / PDL are taken from the candles CME would have printed
- a higher-timeframe swing whose candle opened while CME was shut is dropped. The candles around it are still the perp's own, so a weekend spike can occasionally stop a Friday high from counting as a swing

It is on by default ("Hold perpetual futures to the CME clock" in group 3), with the same symbol list and setting name as Dealing Range Liquidity Reversal. A true crypto perp is never clocked, and futures charts are never affected. If your perp is on another exchange, it is not on the list and runs 24/7.

## Table

Range state, range high and low, delivery, equilibrium, position, last sweep, draw, risk (1R), and POC and value area when volume context is on. Off by default.

## Alerts

| Alert | Fires when |
|---|---|
| Dealing range framed | The first pair completes |
| Dealing range reframed | A later sweep changes the pair |
| Boundary extended | The tracked side reaches a new extreme |
| Draw reached | The exact pool that was the draw is swept |
| BSL swept / SSL swept | Any registered pool on that side is taken |
| Delivery range formed | A raid is followed by a close through structure |
| IPDA Frame contracted | A boundary ages out of the lookback. A time event, not a liquidity event |

Sweeps and ranges are all decided on closed candles.

## What it does not do

- It does not call direction.
- It does not detect consolidation. I tried, against consolidations I marked by hand, and swings plus ATR could not tell a range from a trend reliably enough to trust. Every registered sweep reaches the range engines.
- It does not guess the order when one candle takes both sides.
- It does not treat a range boundary as unswept liquidity.
- It does not see past the history TradingView has loaded. A 40 or 60 day IPDA frame may not be available on a 1m or 5m chart.
- The odds figure is a no-edge baseline. It is not the probability your trade works.

## Install

Copy `MTF_Liquidity_Dealing_Range.pine` into the Pine Editor and add it to the chart. No dependencies.

If a slow chart hits calculation limits with volume context on, lower "Max lookback" in group 9.

## Credit

The dealing-range model and the 20 / 40 / 60 day IPDA data ranges are ICT's, from the 2022 Mentorship.

The register layout (five timeframe slots, sessions, merged tags) was modelled on the LQ MTF liquidity indicator. Wick-exact anchoring, per-tag trimming and drawing-object reuse came from studying Zeiierman's MTF Liquidity Stack. The volume profile's ATR binning follows BigBeluga's Structural Liquidity & POC Matrix. The footprint request follows ata_sabanci's Volume Footprint (MPL-2.0). The odds layer follows the first-passage pricing in The Distilled Odds. The delivery range came out of @Ed_tradess's notes on selecting the range from the actual delivery.

## Licence

MPL-2.0. See `LICENSE`.

## Disclaimer

This is a charting tool. It is not financial advice and it does not make trading decisions.
