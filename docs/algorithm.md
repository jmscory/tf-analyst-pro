# Algorithm Documentation - TF-Analyst Pro

This document provides a comprehensive breakdown of the TF-Analyst Pro algorithm, including calculation methodologies, parameter effects, and implementation details.

## Algorithm Overview

TF-Analyst Pro uses a multi-layered approach that combines technical analysis, fundamental analysis, and position-aware logic to generate intelligent trading recommendations. The algorithm adapts its behavior based on the user's current position status and risk tolerance.

## Core Architecture

The algorithm follows this flow:

Input Layer (User Preferences + Market Data) → Position-Aware Logic Engine → Technical Analysis Module + Fundamental Analysis Module → Weighted Composite Score (0-100) → Position-Adjusted Thresholds → Signal Generation + Risk Management → Price Targets + Alerts + Visualization

## Technical Analysis Component

The technical score is calculated from five equally weighted components, each contributing 20% to the final technical score:

### 1. Trend Analysis (20% of Technical Score)

The trend analysis uses a moving average crossover system with price confirmation:

```pinescript
smaShort = ta.sma(close, 50)  // Default: 50-period SMA
smaLong = ta.sma(close, 200)  // Default: 200-period SMA

trendScore = if smaShort > smaLong and close > smaShort
    1.0  // Strong bullish trend
else if smaShort > smaLong and close <= smaShort
    0.75 // Bullish trend with pullback
else if math.abs(smaShort - smaLong) / math.max(math.abs(smaLong), 1e-6) < 0.01
    0.5  // Neutral/sideways trend
else
    0.0  // Bearish trend
```

**Scoring Logic:**
- **Score 1.0**: Price > SMA50 > SMA200 (classic golden cross with price confirmation)
- **Score 0.75**: SMA50 > SMA200 but price below SMA50 (healthy pullback in uptrend)
- **Score 0.5**: SMAs within 1% of each other (indecision/consolidation phase)
- **Score 0.0**: SMA50 < SMA200 (death cross pattern indicating bearish trend)

### 2. Momentum Analysis (20% of Technical Score)

The momentum component uses MACD histogram analysis to capture momentum shifts:

```pinescript
[macdLine, macdSignal, macdHist] = ta.macd(close, 12, 26, 9)
macdPrev = nz(macdHist[1], 0)

momentumScore = macdHist > 0 ? (macdHist > macdPrev ? 1.0 : 0.85) : 0.0
```

**Algorithm Logic:**
- **Score 1.0**: MACD histogram positive AND increasing (accelerating bullish momentum)
- **Score 0.85**: MACD histogram positive BUT decreasing (decelerating but still bullish momentum)
- **Score 0.0**: MACD histogram negative (bearish momentum)

The algorithm uses the MACD histogram rather than traditional line crossovers because it shows the rate of change of momentum, providing earlier signals of momentum shifts.

### 3. Trend Strength Analysis (20% of Technical Score)

This component uses RSI as a proxy for ADX to measure trend strength:

```pinescript
rsiVal = ta.rsi(close, 14)
trendStrengthVal = math.abs(rsiVal - 50)

trendStrengthScore = trendStrengthVal >= 25 ? 1.0 : 
                    (trendStrengthVal >= 20 ? 0.6 : 0.0)
```

**Rationale:** Strong trends push RSI away from the neutral level of 50, while weak or choppy markets keep RSI oscillating around 50.

**Scoring Logic:**
- **Score 1.0**: |RSI - 50| ≥ 25 (strong trend, RSI ≥ 75 or ≤ 25)
- **Score 0.6**: |RSI - 50| ≥ 20 (moderate trend strength)
- **Score 0.0**: |RSI - 50| < 20 (weak trend, choppy market conditions)

### 4. Volume Confirmation (20% of Technical Score)

Volume analysis confirms the validity of price movements:

```pinescript
volSMA = ta.sma(volume, 20)
volScore = volume > volSMA ? 1.0 : (volume > volSMA * 0.8 ? 0.6 : 0.0)
```

**Scoring Logic:**
- **Score 1.0**: Current volume > 20-period average (strong institutional participation)
- **Score 0.6**: Current volume > 80% of 20-period average (moderate confirmation)
- **Score 0.0**: Current volume ≤ 80% of average (weak confirmation, potential false breakout)


### 5. Volatility Analysis (20% of Technical Score)

Bollinger Bands width analysis measures market volatility and potential for significant moves:

```pinescript
basis = ta.sma(close, 20)
dev = ta.stdev(close, 20)
upper = basis + 2.0 * dev
lower = basis - 2.0 * dev

bbWidth = (upper - lower) / math.max(basis, 1e-6)
bbWidthAvg = ta.sma(bbWidth, 50)

volatilityScore = bbWidth > bbWidthAvg ? 1.0 : 
                 (bbWidth > bbWidthAvg * 0.7 ? 0.6 : 0.3)
```

**Algorithm Rationale:** Higher volatility often precedes significant price moves, while compressed volatility (tight Bollinger Bands) suggests limited opportunity.

**Scoring Logic:**
- **Score 1.0**: BB width > 50-period average (high volatility, good trading opportunities)
- **Score 0.6**: BB width > 70% of 50-period average (moderate volatility)
- **Score 0.3**: BB width ≤ 70% of average (low volatility, compressed range)

### Technical Score Calculation

```pinescript
techScore = (trendScore + momentumScore + trendStrengthScore + volScore + volatilityScore) / 5.0
techScorePct = techScore * 100.0
```

The final technical score is the simple average of all five components, converted to a 0-100 scale.

## Fundamental Analysis Component

The fundamental analysis evaluates five key financial metrics with sector-specific adjustments:

### Fundamental Scoring Function

```pinescript
f_mapFundScore(eps_growth_pct, rev_growth_pct, pe_rel, de_ratio, roe_pct, 
               preset_tech_bias, preset_value_bias, preset_growth_bias)
```

### Fundamental Score Calculation

```pinescript
fundSum = epsS + revS + peS + deS + roeS
fundScoreFinal = fundSum / 5.0
fundScorePct = fundScoreFinal * 100.0
```

The fundamental score is the simple average of all five components, with sector adjustments applied.

## Position-Aware Logic Engine

This is the core innovation of TF-Analyst Pro. The algorithm adjusts its behavior based on the user's current position status.

### Position Status Detection

```pinescript
isLookingToBuy = currentPosition == "Looking to Buy"
hasSmallPosition = currentPosition == "Already Own - Small Position"
hasFullPosition = currentPosition == "Already Own - Full Position"
lookingToAdd = currentPosition == "Looking to Add More"
```

### Risk-Based Entry Pricing

The algorithm calculates different entry discounts based on risk tolerance:

```pinescript
entryDiscountPct = if riskTolerance == "Conservative"
    conservativeDiscount  // Default: 3%
else if riskTolerance == "Moderate"
    moderateDiscount      // Default: 2%
else
    aggressiveDiscount    // Default: 1%
```

### Position-Aware Entry Price Calculation

```pinescript
baseEntryPrice = close * (1 - entryDiscountPct/100)
supportLevel = math.min(lower, recentLow)  // BB lower band or recent low

recommendedBuyPrice = if isLookingToBuy
    math.min(baseEntryPrice, supportLevel)  // Wait for best entry
else if hasSmallPosition or lookingToAdd
    close * (1 - entryDiscountPct/2/100)   // Less aggressive discount
else
    close * (1 - entryDiscountPct*1.5/100) // Only on significant dips
```

**Algorithm Logic:**
- **New Buyers**: Get full discount and support-based entries for optimal timing  
- **Small Position Holders**: Get half discount to encourage dollar-cost averaging  
- **Full Position Holders**: Need 1.5x discount to justify additional risk  

### Investment Horizon Adjustments

```pinescript
horizonMultiplier = if investmentHorizon == "Short-term (1-3 months)"
    0.8   // Reduce profit targets for faster turnover
else if investmentHorizon == "Medium-term (3-12 months)"
    1.0   // Standard targets
else
    1.2   // Increase profit targets for long-term holds
```

## Composite Scoring System

### Weighted Final Score

```pinescript
finalScore = techScorePct * techWeight + fundScorePct * fundWeight
finalScore := math.min(math.max(finalScore, 0), 100)
```

Default weights are 50% technical and 50% fundamental, but users can adjust these based on their investment philosophy.

### Position-Aware Threshold Adjustments

```pinescript
adjustedBuyThresh = if hasFullPosition
    buyThresh + 10  // Raise bar for full positions (default 85 threshold)
else if hasSmallPosition
    buyThresh - 5   // Lower bar slightly for small positions (default 70 threshold)
else
    buyThresh       // Standard threshold for new buyers (default 75 threshold)

adjustedSellThresh = if isLookingToBuy
    sellThresh - 5  // More sensitive to sell signals when not holding
else
    sellThresh      // Standard sell threshold (default 30)
```

**Rationale:** Full position holders should have higher standards for additional purchases, while those with small positions get slightly easier criteria to build their position.

## Signal Generation Algorithm

### Primary Signal Conditions

```pinescript
buySignal = finalScore >= adjustedBuyThresh and riskRewardRatio >= 1.5
strongBuySignal = finalScore >= (adjustedBuyThresh + 10) and riskRewardRatio >= 2.0
sellSignal = finalScore <= adjustedSellThresh
strongSellSignal = finalScore <= (adjustedSellThresh - 10)
```

### Position-Specific Recommendations

```pinescript
positionAction = if isLookingToBuy and buySignal
    strongBuySignal ? "STRONG BUY - Great Entry!" : "BUY - Good Entry Point"
else if (hasSmallPosition or lookingToAdd) and buySignal
    "ADD TO POSITION"
else if hasFullPosition and strongBuySignal
    "CONSIDER ADDING (if possible)"
else if sellSignal and not isLookingToBuy
    strongSellSignal ? "SELL - Take Profits" : "CONSIDER SELLING"
else
    "HOLD - Wait for Better Setup"
```

## Risk Management System

### Risk-Reward Ratio Calculation

```pinescript
profitTarget = close * (1 + profitTargetPct * horizonMultiplier/100)
stopLossPrice = close * (1 - stopLossPct/100)
riskRewardRatio = (profitTarget - close) / (close - stopLossPrice)
```

**Minimum Requirements:**
- Standard buy signals require R/R ≥ 1.5
- Strong buy signals require R/R ≥ 2.0

### Support and Resistance Integration

```pinescript
recentHigh = ta.highest(high, 20)
recentLow = ta.lowest(low, 20)
supportLevel = math.min(lower, recentLow)  // Use BB lower or recent low
```

The algorithm uses the lower of Bollinger Band lower boundary or recent 20-period low as support, providing a technical basis for entry recommendations.

## Alert System Algorithm

The strategy generates position-aware alerts with different conditions:

```pinescript
// Strong buy for new positions
alertcondition(strongBuySignal and enableAlerts and isLookingToBuy, 
               title="🔥 STRONG BUY - New Position")

// Add to position alerts
alertcondition(buySignal and enableAlerts and (hasSmallPosition or lookingToAdd), 
               title="📈 ADD TO POSITION")

// Sell alerts (only for existing holders)
alertcondition(sellSignal and enableAlerts and not isLookingToBuy, 
               title="📉 SELL Alert")
```

## Edge Cases and Limitations

### Division by Zero Protection

```pinescript
math.max(math.abs(smaLong), 1e-6)  // Prevents division by zero in trend calculation
math.max(basis, 1e-6)              // Protects BB width calculation
```

### Null Value Handling

```pinescript
macdPrev = nz(macdHist[1], 0)  // Uses 0 if previous MACD histogram is null
```

### Boundary Constraints

```pinescript
finalScore := math.min(math.max(finalScore, 0), 100)  // Constrains score to 0-100 range
epsS := math.min(epsS, 1.0)                          // Prevents component scores > 1.0
```

### Known Limitations

1. **Fundamental Data Dependency**: Currently relies on manual input of fundamental metrics
2. **Position Tracking**: Requires manual updates of position status
3. **Market Regime Sensitivity**: May underperform in highly volatile or unusual market conditions
4. **Sector Bias**: Preset adjustments may not capture all sector nuances
5. **Lag Effects**: Moving averages and other indicators have inherent lag

### Optimization Considerations

The algorithm prioritizes:
- **Computational Efficiency**: Minimizes expensive calculations
- **Memory Management**: Uses `var` declarations for persistent objects
- **Historical Data Limits**: Constrains lookback periods to prevent timeouts
- **Real-time Performance**: Optimized for live trading environments

This comprehensive algorithm provides a sophisticated yet practical approach to position-aware trading decisions, combining the objectivity of technical analysis with the fundamental value assessment and the personalization of position-aware logic.
