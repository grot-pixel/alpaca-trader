🤖 Alpaca Multi-Strategy Trading Bot (LIVE)[!CAUTION]USE AT OWN RISK — UNTESTEDThis repository executes live trades with real capital via GitHub Actions. It is completely untested in live production conditions. Automated trading bots can lose capital rapidly due to API hiccups, slippage, or market volatility. You assume 100% responsibility for any financial losses.Automated intraday trading bot for Alpaca Markets, running serverlessly via GitHub Actions (no VPS needed). Supports up to 2 accounts and sends a daily email report at market close.What's New (Live v2)Paper InstanceLive Instance (This Repo)EnvironmentPaper SandboxLive Production ⚠️Base URL[https://paper-api.alpaca.markets](https://paper-api.alpaca.markets)[https://api.alpaca.markets](https://api.alpaca.markets) ✅CapitalSimulatedReal Money ✅Libraryalpaca-pyalpaca-py ✅StrategiesSMA + RSI + MACD + VWAPSMA + RSI + MACD + VWAP ✅How It WorksEach GitHub Actions run (every 5 min during market hours):Checks if the market is open — skips if notReads live account equity and applies circuit breakers (daily profit/loss limits)Sweeps open positions for stop-loss and take-profit exitsScans all symbols for buy/sell signals using 4 indicatorsSubmits limit orders for the best signals with real fundsSignal LogicA BUY fires when 2 of 4 indicators are bullish:#IndicatorBullish condition1SMA crossoverFast SMA (8) above Slow SMA (21), or recent cross2RSIBetween 38–68 (healthy momentum, not overbought)3MACD histogramPositive and expanding (momentum building)4VWAPPrice above today's VWAPA SELL fires on 2-of-4 bearish confirmation (same indicators inverted).Setup for Live Trading1. Clone and push to your live repoBashgit clone https://github.com/grot-pixel/alpaca-live.git
cd alpaca-live
# Add your code files here, then:
git add -A
git commit -m "live: deploy live trading instance"
git push origin main
2. Add Live GitHub SecretsGo to your live repo → Settings → Secrets and variables → Actions:SecretValueAPCA_API_KEY_1Live Alpaca API keyAPCA_API_SECRET_1Live Alpaca API secretAPCA_BASE_URL_1[https://api.alpaca.markets](https://api.alpaca.markets) (Must be live endpoint)APCA_API_KEY_2(optional) Second account live keyAPCA_API_SECRET_2(optional) Second account live secretAPCA_BASE_URL_2[https://api.alpaca.markets](https://api.alpaca.markets)EMAIL_USERGmail address for reportsEMAIL_PASSGmail App Password3. Enable GitHub ActionsGo to the Actions tab in your live repository → enable workflows if prompted.ConfigurationEdit config.json to customize behavior (ensure your risk sizing is tight for real capital):Code snippet{
  "symbols": ["TQQQ", "SOXL", "NVDA", ...],    // what to trade

  "max_open_positions": 5,     // max simultaneous positions
  "max_position_pct": 0.20,    // max 20% of equity per symbol
  "max_trade_pct": 0.10,       // deploy 10% of equity per signal

  "stop_loss_pct": 0.03,       // 3% stop-loss (always < take_profit!)
  "take_profit_pct": 0.06,     // 6% take-profit = 1:2 risk/reward

  "daily_profit_target_pct": 0.04,    // halt + close all if up 4% today
  "daily_loss_limit_pct": 0.025,      // halt + close all if down 2.5% today

  "signal_threshold": 2,    // indicators needed for a signal (2, 3, or 4)
                            // 2 = more trades, 3 = higher conviction only

  "sma_fast": 8,
  "sma_slow": 21,
  "rsi_period": 10,
  "rsi_overbought": 68,
  "rsi_sell_min": 58,
  "rsi_buy_min": 38
}
Running Locally (Live Environment)Bashpip install -r requirements.txt

# Set LIVE env vars
export APCA_API_KEY_1="your_live_key"
export APCA_API_SECRET_1="your_live_secret"
export APCA_BASE_URL_1="https://api.alpaca.markets"

# Run the bot (WARNING: PLACES REAL TRADES)
python bot.py

# Run the report
python report.py
Risk Management SummaryStop-loss: 3% below entry — hard rule, no exceptionsTake-profit: 6% above entry (2× the stop)Trailing stop: activates when up >5%, locks in ~2.5% minimumMax 5 positions open at once — forces diversificationDaily circuit breakers: closes everything if up 4% or down 2.5%Market-open check: never trades outside market hours⚠️ Ultimate DisclaimerUSE AT OWN RISK — UNTESTED. This software is provided strictly for educational and research purposes. By running this script against the live Alpaca API, you accept full financial accountability for any capital losses incurred.
