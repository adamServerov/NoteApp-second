# NoteApp-second
//@version=5
strategy("Ichimoku Cloud Strategy", overlay=true)

// Define the input variables
tenkanLength = input.int(9, "Tenkan Sen Length", minval=1)
kijunLength = input.int(26, "Kijun Sen Length", minval=1)
senkouBLength = input.int(52, "Senkou B Length", minval=1)
senkouOffset = input.int(26, "Senkou Offset", minval=1)
stopDist = input.float(1, "Stop Distance", minval=0.001)

// Calculate the indicators
tenkanSen = ta.sma((high + low) / 2, tenkanLength)
kijunSen = ta.sma((high + low) / 2, kijunLength)
senkouA = (tenkanSen + kijunSen) / 2
senkouB = ta.sma((high + low) / 2, senkouBLength)[senkouOffset]

// Plot the indicators
plot(tenkanSen, color=color.red)
plot(kijunSen, color=color.blue)
plot(senkouA, color=color.green)
plot(senkouB, color=color.orange)

// Define the long and short signals
bullish = tenkanSen > kijunSen and ta.crossover(tenkanSen, kijunSen) and close > senkouB
bearish = tenkanSen < kijunSen and ta.crossunder(tenkanSen, kijunSen) and close < senkouB

// Enter long or short positions
if bullish
    strategy.entry("Buy", strategy.long)
    strategy.exit("Stop Loss", "Buy", stop=close - stopDist)
if bearish
    strategy.entry("Sell", strategy.short)
    strategy.exit("Stop Loss", "Sell", stop=close + stopDist)
