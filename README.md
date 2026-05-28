# Support & Resistance Breakout Alerts — MQL4 Script

A MetaTrader 4 script that identifies **confirmed swing highs and swing lows** as dynamic support and resistance levels using a bilateral `ConfirmationPeriod` neighborhood scan, and fires real-time breakout alerts when a new swing high or low forms — providing automated detection of evolving market structure breakouts that define key support and resistance transitions on any symbol and timeframe.

---

## Overview

In technical analysis, swing highs and lows are not merely historical curiosities — they define the living support and resistance structure of the market. A prior swing high that price breaks above becomes a resistance-turned-support level; a prior swing low that price breaks below becomes a support-turned-resistance level. Every time a new swing high forms above a prior one, it constitutes a breakout of the prior resistance level; every new swing low below a prior one constitutes a breakout of prior support. By monitoring for newly formed confirmed swing points in real time, this script provides immediate notification of these structural breakouts as they develop, alerting traders to the moment the market's support and resistance map is being redrawn. The bilateral confirmation algorithm — requiring `ConfirmationPeriod` higher bars on both sides of a swing high and lower bars on both sides of a swing low — ensures only genuinely dominant structural pivots trigger alerts, filtering out minor intrabar noise that would generate false structure signals.

> **Note on file naming:** This file is distributed as `Support_Resistance_Breakout_Alerts_001.mq4` but implements a Swing Highs/Lows detection algorithm. The README documents the actual implemented logic and its structural breakout interpretation.

---

## Features

- **Bilateral `ConfirmationPeriod` swing validation** — nested `j = 1` to `ConfirmationPeriod` loop checks `iHigh(i) > iHigh(i − j)` AND `iHigh(i) > iHigh(i + j)` simultaneously for swing highs; symmetric for swing lows — any false breaks the `isSwingHigh/Low` flag
- **Lookback-windowed scan** — outer `i = ConfirmationPeriod` to `LookbackPeriod` iterates all candidate bars; returns first confirmed swing or `0.0` if window contains no dominant pivot
- **`lastSwingHigh` / `lastSwingLow` deduplication** — alerts fire only when `swingHigh != lastSwingHigh && swingHigh != 0` — prevents repeat alerts on the same structural level across consecutive monitoring cycles; state updated immediately on each new detection
- **Independent bilateral tracking** — `lastSwingHigh` and `lastSwingLow` maintained independently; a new swing low detection does not interfere with swing high state
- **Three notification channels:** sound alert, email, and mobile push
- **Lightweight loop** — polls once per minute (`Sleep(60000)`)
- Alert message includes the confirmed swing price level with symbol and timeframe for immediate structural context

---

## How It Works

1. Every minute, `DetectSwingHigh()` and `DetectSwingLow()` independently scan the lookback window using bilateral `j` confirmation loops
2. Returned values compared against `lastSwingHigh` / `lastSwingLow`:
   - New non-zero swing high → **New Swing High Detected**; `lastSwingHigh` updated
   - New non-zero swing low → **New Swing Low Detected**; `lastSwingLow` updated

---

## Input Parameters

| Parameter            | Type            | Default     | Description                                                        |
|----------------------|-----------------|-------------|--------------------------------------------------------------------|
| `TradeSymbol`        | string          | `EURUSD`    | Symbol for analysis                                                |
| `Timeframe`          | ENUM_TIMEFRAMES | `PERIOD_H1` | Timeframe for swing detection                                      |
| `LookbackPeriod`     | int             | `10`        | Maximum bars back to scan for swing candidates                     |
| `ConfirmationPeriod` | int             | `3`         | Bars required on each side to confirm a swing point                |
| `EnableAlerts`       | bool            | `true`      | Fire an on-screen/sound alert                                      |
| `EnableEmail`        | bool            | `false`     | Send an email notification                                         |
| `EnablePush`         | bool            | `false`     | Send a mobile push notification                                    |

---

## Alert Message Format

```
New Swing High Detected detected on EURUSD (Timeframe: PERIOD_H1)
Price: 1.08640
```

---

## Installation

1. Copy `Support_Resistance_Breakout_Alerts_001.mq4` to `MQL4/Scripts/`
2. Compile in MetaEditor (F7)
3. Drag onto any chart from Navigator → Scripts
4. Configure inputs and click **OK**

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
