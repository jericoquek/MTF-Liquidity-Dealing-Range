[README.md](https://github.com/user-attachments/files/33212426/README.md)
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

**Volume context.** Optional. A volume profile and point of control anchored to the range.

**Ranging box.** Optional and experimental. A dotted early box warns that a consolidation may be forming. A confirmed box marks one that has, and keeps the sweeps inside it from moving the range. See below.

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

## Settings

| Group | What is in it |
|---|---|
| 1. Timeframes | Five swing sources (1H, 4H and Daily on; Weekly and 15m off), levels kept per tag, swing length |
| 2. Sessions | Three sessions with editable names and times, New York time. Off by default |
| 3. Daily levels | PDH / PDL from the completed daily bar. Daily reset |
| 4. Register style | Line style, swept-level history, origin labels, hide levels beyond a distance, merge tolerance |
| 5. Dealing range | The three ranges, primary selector, labels, colours, premium / discount box |
| 6. Delivery range | Toggle and style |
| 7. Draw | Mark the draw |
| 8. Odds | Risk from an ATR multiple, manual points or the last swing. Round-trip cost. How far out to price |
| 9. Volume context | POC line, profile, row height, lookback |
| 10. Ranging | See below |
| 11. Table | Position and size |

Only sources at or above the chart timeframe are used. On a 1H chart that is 1H, 4H and Daily. On a 4H chart, 4H and Daily. A source finer than the chart is skipped, not approximated.

## Ranging (experimental)

Off by default. This is my rule, not ICT's.

Consolidations have cost me dearly, and the first version of this feature never fired once in daily use. It was rebuilt against consolidations I marked by hand, then rebuilt again after the early version boxed trends on a 15m chart.

**What counts as ranging.** Candles building sideways and tight against a level that keeps holding, printing relative equal highs and relative equal lows. In the script that is the full count:

- at least two swing highs at one level in the top third of the box, and two swing lows at one level in the bottom third, on the chart's own timeframe
- "at one level" means within the equal tolerance of each other, 0.5 × ATR by default
- the whole box, wick to wick, no taller than 3.5 × ATR
- two highs only count as two if price pulled back a third of the box between them, and the same for lows
- a spike past the level can sit inside the box, so support that keeps holding under a stray wick still counts, unless a displacement candle printed the spike

**Steps are not ranges.** When one box forms after another and both its high and low have moved the same way, the two overlap, and price never went far past the old box's other side in between, they are steps in a trend. Neither is shown, and neither holds back sweeps from the range. This is fixed, not a setting. The first step can only be recognised when the second one forms, so it shows as ranging until then.

**Two boxes.**

- *Confirmed.* The full count has printed. Drawn in the style you pick, labelled `Ranging`. This is the only box that affects the dealing range.
- *Early.* Dotted by default, with its own colour and border style, and a warning only. It says a consolidation may be forming. It is replaced by the confirmed box when the full count prints, and withdrawn when price breaks it or twelve candles pass without confirmation.

An early box comes from one of two routes, both on by default:

- *After displacement.* The box begins within six candles after a displacement candle and is one touch short of the count. Labelled `Ranging (early)`.
- *Tight bodies.* The bodies of the last six candles sit inside a band of 1 × ATR, with one equal high and one equal low. Labelled `Ranging (tight)`.

No early box may contain a displacement candle. Bodies only decide when it may appear. Every box runs from the highest high to the lowest low.

**How a box ends.** A wick that pokes an edge by less than the wick tolerance, 0.25 × ATR by default, is one more equal high or low, and the box stretches to it. That tolerance is measured from the edge the box started with, so repeated pokes cannot walk it into a breakout. A candle close beyond an edge, or a wick that goes further than the tolerance, ends the box on the candle before.

**What it does to the range.** While a confirmed box is live, sweeps of pools strictly inside it do not reach Rotation, IPDA Frame or Delivery. The register still marks them swept. The sweep that breaks the box passes through as the first side. An early box changes nothing.

**How often the early box is right.** Most of the time it is not, and that is the price of the warning. I measured it on one symbol:

| Chart | Early boxes | Went on to confirm | Warning before confirmation |
|---|---|---|---|
| 15m, 22 days | 68 | 8, about 1 in 8 | 5 candles |
| 5m, 3 days | 19 | 7, about 1 in 3 | 7 candles |

A withdrawn early box usually lasted a few candles, never more than twelve. Nothing I tested could tell a failing one sooner than the break itself without also withdrawing real ranges. A range making its first move from the low to the high looks the same as an uptrend until it turns back or closes outside.

The confirmed box is the reliable one and the late one. On the 5m sample it showed about 80 minutes into the consolidation.

**What else to expect.**

- With "Show withdrawn" off, the chart keeps only what confirmed, so history looks cleaner than it was live. Turn it on to see every warning that was given.
- The width and tolerance defaults were fitted to seven hand-marked consolidations on a 5m chart. Check them on your own market.
- Swing length 1 shows boxes sooner and produces more of them.

| Setting | Default |
|---|---|
| Swing length | 2 |
| Max width (x ATR) | 3.5 |
| Min swings per side | 2 |
| Equal tolerance (x ATR) | 0.5 |
| Wick tolerance (x ATR) | 0.25 |
| Early after displacement, body x ATR | On, 1.5 |
| Early on tight bodies, candles / band x ATR | On, 6 / 1.0 |
| Withdraw early box after (candles) | 12 |
| Early box, colour and border style | Dotted |
| Show withdrawn | Off |
| Show box, colour and border style | On, solid |
| Boxes shown | 5 |

## Table

Range state, range high and low, delivery, equilibrium, position, last sweep, draw, risk (1R), and POC when volume context is on. Off by default.

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
| Ranging early | A dotted early box appears. Not confirmed |
| Ranging confirmed | The full count prints and the confirmed box goes live |
| Ranging withdrawn | An early box is broken or runs out of time |
| Ranging broken - BSL / SSL | Price closes beyond the confirmed box or runs through an edge |

Sweeps, ranges and ranging boxes are all decided on closed candles.

## What it does not do

- It does not call direction.
- It does not guess the order when one candle takes both sides.
- It does not treat a range boundary as unswept liquidity.
- It does not see past the history TradingView has loaded. A 40 or 60 day IPDA frame may not be available on a 1m or 5m chart.
- The odds figure is a no-edge baseline. It is not the probability your trade works.

## Install

Copy `MTF_Liquidity_Dealing_Range.pine` into the Pine Editor and add it to the chart. No dependencies.

If a slow chart hits calculation limits with volume context on, lower "Max lookback" in group 9.

## Credit

The dealing-range model and the 20 / 40 / 60 day IPDA data ranges are ICT's, from the 2022 Mentorship.

The register layout (five timeframe slots, sessions, merged tags) was modelled on the LQ MTF liquidity indicator. Wick-exact anchoring, per-tag trimming and drawing-object reuse came from studying Zeiierman's MTF Liquidity Stack. The volume profile's ATR binning follows BigBeluga's Structural Liquidity & POC Matrix. The odds layer follows the first-passage pricing in The Distilled Odds. The delivery range came out of @Ed_tradess's notes on selecting the range from the actual delivery.

## Licence

MPL-2.0. See `LICENSE`.

## Disclaimer

This is a charting tool. It is not financial advice and it does not make trading decisions.
