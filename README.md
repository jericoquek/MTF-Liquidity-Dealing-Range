[MTF_Liquidity_Dealing_Range_User_Guide.md](https://github.com/user-attachments/files/34166174/MTF_Liquidity_Dealing_Range_User_Guide.md)
# MTF Liquidity Dealing Range: User Guide

## What it does

It marks the liquidity on your chart (swing highs and lows from higher timeframes, session highs and lows, the previous day's high and low) and builds a dealing range from the liquidity that has actually been taken.

A range only appears once price has swept a pool on one side and then the other. Until then there's nothing to measure against, and the indicator draws no range. Once there is one, it shows you whether price is in premium or discount, where equilibrium sits, and which pool is likely next.

It doesn't tell you which way to trade. That's your read. It tells you where price is inside the range liquidity has framed.

## Quick start

1. Add it to a 1m–1H chart. It runs on any timeframe, but it's built for intraday.
2. Leave the defaults alone for your first session.
3. Wait for a range to frame: a **DR HIGH** and a **DR LOW**, with a premium/discount box over them.
4. Read where price is: upper half is premium, lower half is discount, and the middle line is EQ.
5. Look at the **draw**: the nearest untouched pool on the other side of the last sweep.

## What you see on the chart

| On the chart | What it means |
|---|---|
| Thin horizontal lines with tags like `1h`, `4h,Asia`, `PDH` | Liquidity pools, waiting to be taken. The tag says where each came from. Levels at the same price merge into one line. |
| A tag ending in `REH×2` or `REL×3` | Relative equal highs or lows: two or more different candles printed this level. Engineered liquidity. |
| A line that stops at a candle | That pool was swept by that candle. |
| **DR HIGH / DR LOW** | The dealing range, framed by swept liquidity. |
| **DR EQ** and the 0.5 line | Equilibrium, the middle of the range. |
| **PREMIUM / DISCOUNT** box | Above 0.5 is premium, below is discount. |
| Draw marker | The nearest unswept pool on the opposite side of the last sweep. |
| Percentages next to levels | The odds of reaching that level before a 1R stop **with no edge at all**. That's the number your read has to beat. It isn't a forecast. |
| **POC / VAH / VAL** (optional) | Where most of the volume inside the range traded, and the band that holds 70% of it. |

## How to read it

- **The last sweep sets the draw.** If sell-side was just taken, the draw is the nearest buy-side pool above.
- **Premium isn't a short and discount isn't a long.** Take your bias from your own narrative, then use the range to judge whether price is in a good place to act on it.
- **If one candle takes both sides,** the indicator doesn't guess which happened first. It waits for the next one-sided sweep.

## The four ranges

Pick one as **Primary range** in group 5. Everything (the box, EQ, draw, odds, volume, table and alerts) measures against it.

| Range | What builds it | Use it for |
|---|---|---|
| **Rotation** (default) | Any opposing sweep moves that side | Day-to-day trading: where price sits between the last two pools taken |
| **IPDA Frame** | Only sweeps at or beyond the edges, over 20, 40 or 60 days | The bigger picture |
| **Delivery** | A sweep, then a candle close through the last opposing swing | Premium and discount of the current leg, for entering on its pullback |
| **Manual** | Two times you set | Your own range. Set "Manual from" on the low's candle and "Manual to" on the high's candle. |

If the range you picked isn't live yet, it falls back to one that is, and the table marks it "(fallback)".

## Settings in plain words

| Group | What's in it |
|---|---|
| 1. Timeframes | Which higher timeframes supply swing levels (1H, 4H and Daily by default), and how many levels each keeps. Only timeframes at or above your chart's are used. |
| 2. Sessions | Asia (20:00–00:00) and London (02:00–05:00) highs and lows, New York time. New York (09:30–12:00) is available, off by default. |
| 3. Daily levels | Previous day high and low. **CME hours only**: on perps, ignores candles while CME is closed (see below). |
| 4. Register style | How the level lines look. **Merge tolerance** also decides how close two levels must be to count as equal. |
| 5. Dealing range | Which ranges run, which one is primary, the manual range times, colours, the premium/discount box. |
| 6. Delivery range | Switch on the delivery range. |
| 7. Draw | Mark the next pool. |
| 8. Odds | How the odds are priced: risk from ATR, fixed points or the last swing, plus your trading cost. |
| 9. Volume context | POC, value area and volume profile, anchored to the range. Off by default. |
| 10. Table | A summary panel: range state, levels, position, last sweep, draw. Off by default. |

## Recommended starting settings

**For CME futures (MNQ, NQ, ES and so on):**
- Defaults are fine.
- **Merge tolerance:** 4–8 ticks on NQ (1–2 points), so relative equal highs and lows get picked up.

**For perps that track a CME market (XYZ100 and other trade.xyz markets, Binance CL):**
- Keep **CME hours only** ticked. Weekend and daily-halt candles then don't sweep levels or move the range, and PDH/PDL follow the CME day. It has no effect on futures, so you can leave it ticked everywhere.

**Volume context (group 9), if you use it:**

| Chart | Ticks per row on XYZ100 | Ticks per row on NQ | Max lookback |
|---|---|---|---|
| 1m | 1 | 2 | 1900 |
| 5m | 2 | 4 | 1000, raise it if the range is older |
| 15m | 4 | 8 | 1000 |
| 1H | 10 | 20 | 1000 |

- **Value area:** 70% is the classical figure. Lower it (down to 20%) for a tighter core around the POC.
- **Real volume at price** needs TradingView Premium or Ultimate. Leave it off unless you have that plan. Hover over the POC label to see how many candles used real data.

## Common questions

**Why is there no range on my chart?**
Liquidity hasn't framed one yet. It takes a sweep on one side, then the other. Wait for it, or pick Manual.

**My manual range doesn't reach the high I picked.**
The candle holding each time is included. Check the To time is on the candle that made the high. The date pickers may not use New York time, so check that the lines start where you meant.

**"Memory limits exceeded"**
Switch off **Real volume at price** in group 9, or lower **Max lookback**.

**The indicator is slow to load.**
Switch off swing timeframes you don't use, and leave Real volume at price off.

**What does REH×2 mean?**
Two different candles printed that level within the merge tolerance: relative equal highs. The same wick reported by several timeframes counts once.

## What it doesn't do

- It doesn't call direction.
- It doesn't guess which side was taken first when one candle takes both.
- It doesn't treat a range edge as unswept liquidity.
- It can't see further back than the history TradingView has loaded. A 40 or 60 day IPDA frame may not fit on a 1m or 5m chart.
- The odds are a no-edge baseline, not the chance your trade works.

---
Pine Script v6. MPL-2.0. A charting tool, not financial advice.
