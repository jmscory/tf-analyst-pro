# TF-Analyst Pro

<div align="center">
  <img src="assets/images/logo.png" alt="TF-Analyst Pro Logo" width="400"/>
  
  [![Version](https://img.shields.io/badge/version-1.2-blue.svg)](https://github.com/jmscory/tf-analyst-pro)
  [![Pine Script](https://img.shields.io/badge/pine_script-v6-success.svg)](https://www.tradingview.com/pine-script-docs/en/v6/index.html)
  [![License](https://img.shields.io/badge/license-Apache-green.svg)](LICENSE)
</div>

## 🚀 Overview

TF-Analyst Pro is an advanced TradingView strategy designed to provide intelligent trading signals based on your current position status, risk tolerance, and investment horizon. Unlike traditional indicators that provide generic buy/sell signals, this strategy adapts its recommendations based on whether you're looking to buy, already own a position, or looking to add more shares.

## ✨ Key Features

### Position-Aware Intelligence
- **Smart Positioning**: Adapts recommendations based on your current holdings
- **Risk-Based Entry Prices**: Calculates optimal entry points based on your risk tolerance
- **Position-Specific Actions**: Different strategies for new buyers vs. existing holders

### Advanced Technical Analysis
- **Multi-Indicator Scoring**: Combines trend, momentum, volatility, and volume analysis
- **Dynamic Thresholds**: Position-aware buy/sell thresholds
- **Support/Resistance Integration**: Uses Bollinger Bands and price action for entry timing

### Fundamental Integration
- **Growth Metrics**: EPS and revenue growth analysis
- **Valuation Assessment**: P/E ratio evaluation relative to sector
- **Financial Health**: Debt-to-equity and ROE analysis
- **Sector Presets**: Optimized scoring for Technology, Financials, Value, and Growth stocks

### Risk Management
- **Position Sizing**: Built-in risk tolerance adjustment
- **Stop Loss & Profit Targets**: Automatic calculation based on volatility
- **Risk/Reward Ratios**: Ensures favorable risk-adjusted returns

## 📊 Algorithm Breakdown

The strategy uses a composite scoring system that combines:

1. **Technical Score (50% default weight)**:
   - Trend Analysis (SMA 50/200 crossover)
   - Momentum (MACD histogram)
   - Trend Strength (RSI-based)
   - Volume Confirmation
   - Volatility Assessment (Bollinger Bands)

2. **Fundamental Score (50% default weight)**:
   - EPS Growth Rate
   - Revenue Growth Rate
   - P/E Ratio vs. Sector
   - Debt-to-Equity Ratio
   - Return on Equity (ROE)

3. **Position-Aware Adjustments**:
   - Threshold modifications based on current position
   - Entry price calculations with risk-based discounts
   - Investment horizon multipliers

## 🛠 Installation & Setup

1. **Copy the Strategy**:
   - Open TradingView Pine Editor
   - Paste the complete Pine Script code
   - Save as "TF-Analyst Pro"

2. **Configure Your Settings**:
3. **Customize Parameters**:
   - Adjust technical indicator periods
   - Set profit targets and stop losses
   - Configure fundamental fallback values

## 🎯 Usage Examples

### New Investor (Looking to Buy)
- Strategy waits for optimal entry points with higher discounts
- Provides "STRONG BUY" signals only at favorable risk/reward ratios
- Conservative position sizing recommendations

### Existing Holder (Small Position)
- "ADD TO POSITION" signals for dollar-cost averaging
- Reduced entry discount requirements
- Balanced risk management

### Full Position Holder
- Higher thresholds for additional buys
- Enhanced sell signal sensitivity
- Focus on profit-taking opportunities

## 📈 Signal Interpretation

| Signal | Description | Action |
|--------|-------------|--------|
| 🔥 STRONG BUY | Score ≥85, R/R ≥2.0 | High confidence entry |
| 🚀 BUY | Score ≥75, R/R ≥1.5 | Good entry opportunity |
| 📈 ADD | Position-specific buy | Increase holdings |
| 📊 HOLD | Score 30-75 | Wait for better setup |
| 📉 SELL | Score ≤30 | Consider profit-taking |

## ⚙️ Configuration Options

### Risk Management
- **Conservative**: 3% entry discount, higher thresholds
- **Moderate**: 2% entry discount, balanced approach
- **Aggressive**: 1% entry discount, lower thresholds

### Investment Horizons
- **Short-term**: 0.8x profit multiplier, faster signals
- **Medium-term**: 1.0x standard settings
- **Long-term**: 1.2x profit multiplier, patient approach

## 🚨 Alert System

The strategy includes comprehensive alert conditions:
- Position-specific buy/sell alerts
- Price target notifications
- Risk management warnings
- Custom alert messages with dynamic values

## 📊 Visual Indicators

- **Score Plot**: Real-time composite score with color coding
- **Background Coloring**: Visual signal strength indication
- **Price Target Lines**: Entry, profit, and stop-loss levels
- **Information Labels**: Position status and recommendations

## 🗺 Roadmap

### Version 2.0 (Planned)
- [ ] Real-time fundamental data integration
- [ ] Machine learning signal enhancement
- [ ] Multi-timeframe analysis
- [ ] Portfolio-level position management

### Version 2.1 (Future)
- [ ] Sector rotation signals
- [ ] Options strategy integration
- [ ] Performance analytics dashboard
- [ ] Social sentiment analysis

## 🔧 Customization

The strategy is highly customizable with 25+ input parameters:
- Technical indicator periods
- Risk management settings
- Fundamental scoring weights
- Alert preferences
- Visual display options

## ⚠️ Important Notes

- **Fundamental Data**: Currently uses fallback manual inputs for fundamental metrics
- **Position Tracking**: Manual position status updates required
- **Risk Disclaimer**: This is for educational purposes; always do your own research
- **Backtesting**: Test thoroughly before live trading

## 📄 License

This project is licensed under the Apache License - see the [LICENSE](LICENSE) file for details.

## 📚 Documentation

- [Algorithm Details](/docs/algorithm.md) - Deep dive into the calculation methodology
- [Project structure](/docs/project_strucure.md) - Strucutre of repo  

## 🆘 Support

For questions, bug reports, or feature requests:
- Open an issue on GitHub
- Review the documentation
- Check existing discussions

---

**Disclaimer**: The information provided in this trading strategy is for educational and informational purposes only and should not be construed as financial, investment, or trading advice. Nothing contained herein constitutes a recommendation to buy, sell, or hold any security, financial product, or instrument. You are solely responsible for your own investment decisions and any resulting financial losses. Past performance is not indicative of future results. Trading involves significant risk, including the possible loss of your entire investment. Before making any financial or investment decisions, you should conduct your own research and seek advice from a licensed financial advisor or other qualified professional. The author/publisher assumes no responsibility or liability for any errors, omissions, or outcomes resulting from the use of this information.