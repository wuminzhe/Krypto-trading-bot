# What is Market Making?

做市是一种交易策略，交易者同时下买入和卖出订单，试图从买卖价差中获利。做市商随时准备从其他交易员那里买入和卖出，从而为市场提供流动性。

这个策略对交易者很有吸引力，因为它不要求交易者对市场有一个方向性的看法——当市场上涨和市场下跌时，都有钱可以赚。寻找流动性和交易量的交易所也极大地激励了这一点——许多交易所的运营商会付钱让你在交易所做市。

# An example

让我们考虑一个简化的市场。假设有三个交易者：爱丽丝、鲍勃和蒂姆。爱丽丝想卖掉她的一些比特币，鲍勃想把他的一些美元兑换成比特币。两个人都不了解市场或加密货币——他们使用比特币，但不会因为试图获得绝对最佳价格而失眠。蒂姆正在经营 Krypto trading bot。现在假设BTC对美元的价格是100美元。蒂姆可以配置krypto trading bot以95美元的价格发送1个BTC的买入订单，以105美元的价格发送1个BTC的卖出订单。蒂姆希望鲍勃能以105美元的价格来购买所提供的销售订单，爱丽丝将以95美元的价格来出售BTC，即蒂姆赚了10美元的价格。

但如果那不发生呢？如果比特币/美元的价格跳到103美元怎么办？现在蒂姆的订单看起来没有竞争力，只有95美元——爱丽丝不想卖那么低。另外，鲍勃只需再多付两美元就能得到一笔可观的交易。为了防止出现这种情况，蒂姆的Krypto Trading Bot将通过取消95-105美元的订单，并将新的一组订单（也称为做市）调整为98-108美元。

像现在任何市场一样，鲍勃和爱丽丝可能不是人类。他们当然不是在曼哈顿下城地板上叫喊的人。更可能的是，它们是能够以毫秒为单位下订单的计算机算法。要想作为一个做市商生存下去，你需要比那些算法更快地赚钱。

# So, how does **Krypto-trading-bot** work?

如前所述，做市实际上是计算出某物的价格，然后围绕该价格做市的艺术。那么我们怎么知道比特币的真实价格是多少呢？好。。。当然，比特币的价格现在可能与一天、一小时甚至一秒钟后的价格有很大的不同。我们能做的最好的就是对比特币的价格进行估计，或者说建立一个公允价值。在Krypto Trading Bot中，我们以发送订单的交易所的市场数据为起点。这包括市场中其他参与者的最佳出价和报价以及最近的交易（即市场交易）。

根据我们的公允价值，我们需要围绕这个价格建立一个市场。回到我们假设的鲍勃和爱丽丝的例子：我们可以把我们的市场定为95-105美元，我们也可以定为99.99-101.01美元，或者我们也可以定为47-332.21美元。那么我们如何决定呢？这就是引用参数发挥作用的地方。这些参数决定了一个市场的广度（广度=askprice-biddprice），以及我们希望我们在市场上的报价规模。提出有利可图的参数的程序既是一门艺术，也是一门科学——没有一个适合所有情况的公式。

当krypto trading bot找到合适的市场时，krypto trading bot将发送买卖订单。希望它能低买高卖，每天重复多次。有时情况并非总是如此——有时按照你设定的价格卖家比买家多。通常情况下，当市场向一个方向快速移动时，就会出现这种情况。幸运的是，Krypto Trading Bot将阻止您在没有找到相应买家的情况下销售过快，并停止发送订单来防止向不平衡（imbalanced side）的方向发展。

# How do I see what's going on?

Navigate to the Web UI as described in the install process. You should see a screen like:

![](https://user-images.githubusercontent.com/1634027/44740610-2f302e00-aafb-11e8-8066-e4362320c62e.png)

* Market Data and Quotes - this is perhaps the most important screen in the app.

  * The top row in blue with "q" is the quote that you generate via the Quoting Parameters supplied. If the text is grey, the generated quotes are not in the market, and green when they are.

  * "FV" is the fair value price calculated by **Krypto-trading-bot** as the starting point for the generated quote.

  * The "mkt" rows are the best bids and offers on the exchange you are connected to.

* Trades - Trades done by your exchange account. "side" is the side which your order was sent as. "val" is the total value of the trade, which is price * size +/- total exchange fee

* Market Trades - Trades done by all participants in the market. "ms" is the side that was the make side (provided liquidity). The columns starting with "q" are your quotes at the time of the trade, the columns starting with "m" are the best bid and offer information at the time of the trade.

* Quoting Parameters - All of the parameters needed to generate a quote. See the section "How do I control **Krypto-trading-bot**' quotes?" for each field's description.

* Positions - Shows holdings of each currency pair you are using.

* Order List - Shows order statuses of each order sent to the exchange.

  * "Cxl" - clicking the red button will attempt to cancel the order.

# How do I control **Krypto-trading-bot**' quotes?

In the web UI, there are three rows of panels with cryptic looking names and editable textboxes. Those are the quoting parameters, the knobs which we can turn to affect how **Krypto-trading-bot** will trade.

* `%` - If enabled, the values of `bidSize`, `askSize`, `tbp`, `pDiv` and `range` will be a percentage related to the total funds (available + held in both sides); useful when the very same funds are used in multiple markets, so the quantity of the funds is highly variable, then may be useful to work with percentages.

* `mode` - Sets the quoting mode

  * `Join` - Sets our quote to be at the best bid and the best offered price, If the BBO is narrower than `width`, set our bid quote at `FV - width / 2` and ask quote at `FV + width / 2`.

  * `Top` - Same as `Join`, but if the code can better the best bid or offer by a penny while respecting the `width`, set that as the quote so we will then be at the top of the market.

  * `Mid` - Set our bid quote at `FV - width / 2` and ask quote at `FV + width / 2`

  * `Inverse Join` - Set the quote at the BBO if the BBO is narrower than `width`, otherwise make the quote so wide that no one will trade with it.

  * `Inverse Top` - Same as `Inverse Join` but make our orders jump to the very top of the order book.

  * `HamelinRat` - Follow the Colossus of the market. Unlike other modes, it does not calculate the quote spread based on fair value, instead it looks for the biggest order in the market levels and places the quote right before it.

  * `Depth` - Use `width` as `depth`. Unlike other modes, it does not calculate the quote spread based on fair value, instead it walks over all current open orders in the book and places the quote right after `depth` quantity, at both sides.

* `safety`- Sets a quoting Safety

  * `PingPong` - Always respect the calculated `widthPong` from the last sold or bought `size`, if any.

  * `PingPoing` - Same as `PingPong` but do not respect always the `widthPong` to make new Pong trades, instead if the `fair value` has moved in a opposite `widthPong` direction, Ping trades will be restarted with 0 price as safety. For example if last buy was at 100 and withpong is 10, last buy safety will be ignored/restarted if price becomes 90.

  * `Boomerang` - Same as `PingPong` but the calculated `widthPong` for new Pongs is based on any best matching (using `pongAt`) previous sold or bought `size`, if any.

  * `AK-47` - Same as `Boomerang` but allows multiple orders at the same time in both sides. To avoid old trades, on every new trade **Krypto-trading-bot** will cancel all previous trades if those are worst.

    * `bullets` - Maximum amount of trades placed in each side (only affects `AK-47`).

    * `range` - Minimum width between `bullets` in USD (ex. a value of .3 is 30 cents; only affects `AK-47`).

* `pingAt` (Pongs are always placed in both sides, only affects `PingPong`, `Boomerang` and `AK-47`)

  * `BothSides` - Place new Pings in both sides.

  * `BidSide` - Place new Pings only in Bid side, and therefore in Ask side only Pongs will be placed.

  * `AskSide` - Place new Pings only in Ask side, and therefore in Bid side only Pongs will be placed.

  * `DepletedSide` - Place new Pings only in the opposite side with not enough funds to continue trading.

  * `DepletedBidSide` - Place new Pings only in the Ask side if there are not enough funds to continue trading in the Bid side.

  * `DepletedAskSide` - Place new Pings only in the Bid side if there are not enough funds to continue trading in the Ask side.

  * `StopPings` - Only place new Pongs based on the history of Pings, without placing new Pings.

* `pongAt` (only affects `Boomerang` and `AK-47`)

  * `ShortPingFair` - Place new Pongs based on the lowest margin Ping in history respecting the `widthPong` from the `fair value`.

  * `LongPingFair` - Place new Pongs based on the highest margin Ping in history respecting the `widthPong` from the `fair value`.

  * `ShortPingAggresive` - Place new Pongs based on the lowest margin Ping in history without respecting the `widthPong` from the `fair value`.

  * `LongPingAggresive` - Place new Pongs based on the highest margin Ping in history without respecting the `widthPong` from the `fair value`.

* `bw?` - Enable Best Width to place orders avoiding "hollows" in the book, while accomodating new orders right near to existent orders in the book, without leaving "hollows" in between.

* `bwSize` - If Best Width is enabled, set the total size of trades in the book to ignore. Set to 0 to only ignore "hollows". Useful for ignoring very small trades in the book.

* `%w?` - If enabled, the values of `width` or `widthPing` and `widthPong` will be a percentage related to the `fair value`; useful when calculating profits subtracting exchange's fees (that usually are percentages too).

* `width` and `widthPing` - Minimum width (spread) of our quote in USD (ex. a value of .3 is 30 cents). With the exception for when `apr` is checked and the system is aggressively rebalancing positions after they get out of whack, `width` will always be respected.

* `widthPong` - Minimum width (spread) of our quote in USD (ex. a value of .3 is 30 cents). Used only if previous Pings exists in the opposite side.

* `orderPctTot` - If `%` is enabled, specify the method for calculation of `bidSize` and `askSize` as percentages.

  * `Value` - Percentage is taken of the total funds (available funds + held in both sides). For example, if 20% is set, and the total funds is $100, then the maximum bid size is $20.  This has a similar effect as to non-percentage-based sizes, but allows for bid sizes to adjust to funds quantity.

  * `Side` - Percentage is taken of funds only on one side.  For example, if 20% is set, and quote funds are worth 20$, and base funds are worth $80, then buys will placed for 4$ and sells will placed for $16.  This allows trading to continue even when funds on one side are heavily depleted, but results in held balances trending towards being evenly distributed.

  * `TBPValue` - Percentage is taken of the total funds as in `Value`, but either the sell size or buy size is shrunk proportional to the value of the TBP, such that the balances will move towards being bought or sold depending on which side the TBP is.

  * `TBPSide` - Percentage is taken of funds only on one side as in `Side`, but balance is taken relative to the `pDiv` extents such that they are not crossed, and either the sell size or buy size is shrunk proportional to the distance from the TBP, such that balances will tend to migrate towards the TBP.

    * `exp` - If `TBPSide` is used for `orderPctTot`, this specifies the exponent to raise the size multipliers by.  The higher the number, the more the sizes will shrink as balance passes the TBP.

* `bidSize` - Maximum bid size of our quote in BTC (ex. a value of 1.5 is 1.5 bitcoins). If `%` is enabled, then this is the maximum bid size as a % as specified in `orderPctTot`. With the exception for when `apr` is checked and the system is aggressively rebalancing positions after they get out of whack.

* `askSize` - Maximum ask size of our quote in BTC (ex. a value of 1.5 is 1.5 bitcoins). If `%` is enabled, then this is the maximum ask size as a % as specified by `orderPctTot`. With the exception for when `apr` is checked and the system is aggressively rebalancing positions after they get out of whack.

* `maxBidSize?` and `maxAskSize?` - Use `bidSize` and `askSize` as minimums and automatically find the maximum possible `size` based on the current "Target Base Position" (just as having enabled `apr` on `Size` but even before your position diverges more than `pDiv`).

* `fv` - Sets the fair value calculation mode

  * `BBO` - `FV = ([topBid price] + [topAsk price]) / 2.0`

  * `wBBO` - `FV = ([topBid price]*[topBid size] + [topAsk price]*[topAsk size]) / ([topAsk size] + [topBid size])`

  * `rwBBO` - `FV = ([topBid price]*[topAsk size] + [topAsk price]*[topBid size]) / ([topAsk size] + [topBid size])`

* `apMode`

  * `Manual` - **Krypto-trading-bot** will not try to automatically manage positions, instead you will need to manually set `tbp`.

  * `EWMA_LS` - **Krypto-trading-bot** will use a `long` minute and `short` minute exponential weighted moving average calculation to buy up BTC when the `short` minute line crosses over the `long` minute line, and sell BTC when the reverse happens. The EWMA values are currently exposed in the stats.

  * `EWMA_LMS` - **Krypto-trading-bot** will use a `long` minute, `medium` minute and `short` minute exponential weighted moving average calculation, together with the simple moving average of the last 3 `fair value` values, to buy up BTC when the `short` minute line crosses over the `long` minute line, and sell BTC when the reverse happens.

  * `EWMA_4` - **Krypto-trading-bot** will use a `medium` minute and `small` minute EWMA calculation to buy when the `small` minute line crosses over the `medium` minute line, and sell when the reverse happens. Additionally sets the `tbp` to 0% if the `verylong` EWMA minute line crosses over the `long` EWMA minute line.

  * `short` - Used when `apMode` is `EWMA_LS`, `EWMA_LMS` or `EWMA_4`. Sets the periods of EWMA Short to automatically manage positions.
  * `medium` - Only used when `apMode` is `EWMA_LMS` or `EWMA_4`. Sets the periods of EWMA Medium to automatically manage positions.
  * `long` - Used when `apMode` is `EWMA_LS`, `EWMA_LMS` or `EWMA_4`. Sets the periods of EWMA Long to automatically manage positions.
  * `verylong` - Only used when `apMode` is `EWMA_4`. Sets the periods of EWMA VeryLong to automatically manage positions.

* `sensibility` - Threshold removed from each period, affects EWMA Long, Medium and Short. The decimal value of `sensibility` must be betweem 0 and 1.

* `tbp` - Only used when `apMode` is `Manual`. Sets a static "Target Base Position" for **Krypto-trading-bot** to stay near. In manual position mode, **Krypto-trading-bot** will still try to respect `pDiv` and not make your position fluctuate by more than that value. So if you have 10 BTC to trade, set `tbp = 3`, set `apMode = Manual`, and `pDiv = 1`, your holding of BTC will never be less than 2 or greater than 4.

* `pDivMode` - Only used when `apMode` is not `Manual` mode. Sets the strategy of dynamically adjusting the `pDiv` depending on the divergence from 50% of Base Value.

  * `Manual` - No dynamic adjusting of `pDiv`.

  * `Linear` - Linear calculation between `pDiv` and `pDivMin`.

  * `Sine` - Calculation between `pDiv` and `pDivMin` on a sine curve.

  * `SQRT` - Square root calculation between `pDiv` and `pDivMin`.

  * `Switch` - If `tbp` is more than 90% or less than 10%, `pDivMin` is taken, otherwhise `pDiv`.

* `pDiv` - If your "Target Base Position" diverges more from this value, **Krypto-trading-bot** will stop sending orders to stop too much directional trading. So if you have 10 BTC to trade, "Target Base Position" is reporting 5, and `pDiv` is set to 3, your holding of BTC will never be less than 2 or greater than 8.

* `pDivMin` - Only used when `pDivMode` is not `Manual`. It defines the minimal `pDiv` for the dynamic positon divergence.

* `apr` - If you're in a state where **Krypto-trading-bot** has stopped sending orders because your position has diverged too far from Target Base Position, this setting will much more aggressively try to fix that discrepancy by placing orders much larger than `size` and at prices much more aggressive than `width` normally allows (see `pongAt` option). It's a bit risky to use this setting.

  * `Off` - **Krypto-trading-bot** will not try to aggressively try to stabilize the target based position.

  * `Size` - **Krypto-trading-bot** will aggressively make use of bigger `size` values (x3 `size` or half of the diverged target base position, whatever is smaller).

  * `SizeWidth` - Same as `Size` but also will aggressively make use of smaller `width` values (respecting always aggressive `pongAt` option and `widthPong`).

* `aprFactor` - Defines the value with which the `size` is multiplicated when `apr` is in functional state.

* `sop` - Super opportunities, if enabled and if the market width is `sopWidth` times bigger than the `width` set, it multiplies `sopTrades` to `trades` and/or `sopSize` to `size`, in both sides at the same time.

* `sopWidth` - Is the value with the market width is multiplicated to define the activation point for Super opportunities.

* `sopTrades` - Multiplicates `trades` to rise the possible Trades per Minute if `sop` is in `Trades` or `tradesSize` state.

* `sopSize` - Multiplicates `width` if `sop` is in `Size` or `tradesSize` state.

* `trades` - Often, only buying or selling many times in a short timeframe indicates that there is going to be a price swing. `trades` and `/sec` are used to limitate ping trades: If you successfully complete more orders than `trades` in `/sec` seconds, the bot will stop sending more buy orders until either `/sec` seconds has passed, or you have sold enough at a higher cost to make all those buy orders profitable. The number of trades is reported by side in the UI; "BuyTS", "SellTS", and "TotTS". If "BuyTS" goes above `trades`, the bot will stop sending buy orders, and the same for sells. For example, if `trades` is 2 and `/sec` is 1800 (half an hour):

Time     | Side | Price | Size | BuyTS | SellTS | Notes
-------- |------|------:|-----:|------:|-------:|------------------------------------------------:
12:00:01 | Buy  | 10    | 1    | 1     | 0      |
12:00:02 | Buy  | 10    | 0.5  | 1.5   | 0      | Partial fills of `size` get counted fractionally
12:00:03 | Sell | 11    | 0.75 | 0.75  | 0      | Sell for more decrements the imbalance
12:00:05 | Sell | 5     | 0.75 | 0.75  | 0      | Sell for less than the other buys doesn't help
12:00:06 | Buy  | 10    | 0.5  | 1.75  | 0      |
12:00:07 | Buy  | 10    | 0.5  | 2.75  | 0      | Stop sending buy orders until 12:30:07!

* `/sec` - see `trades`.

* `ewmaPrice?` - Use a quote protection of `periods` smoothed line of the fair value to limit the price while sending new orders.

* `ewmaWidth?` - Use a quote protection of `periods` smoothed line of the width (between the top bid and the top ask) to limit the widthPing while sending new orders.

* `periodsᵉʷᵐᵃ` - Maximum amount of values collected in the sequences used to calculate the `ewmaPrice?` and `ewmaWidth?` quote protection. After collect sequentially every 1 minute the value of the `fair value`, and before place new orders, a limit will be always applied to the new orders price using a `ewma` calculation, taking into account only the last `periods` periods in each sequence.

* `ewmaTrend?` - Use a trend protection of double `periods` (Ultra+Micro) smoothed lines of the price to limit uptrend sells and downtrend buys.

* `threshold` - When trend stregth is above positive threshold value bot stops selling, when strength below negative threshold value bot stops buying.

* `ultra` - Time in minutes to define Ultra EMA

* `micro` - Time in minutes to define Micro EMA

* `stdev`

  * `Off` - Do not limit the price of new orders.

  * `OnFV` - Use a quote protection of STDEV, calculated from a sequence of `fair value` values during `periods` periods of 1 second, to limit the price equally on both sides while sending new orders.

  * `OnFVAPROff` - Same as `OnFV` when `apr` is `Off` or when the system is not aggressively rebalancing positions; otherwise if is rebalancing, is same as `Off`.

  * `OnTops` - Use a quote protection STDEV, calculated from a unique sequence of both `best bid` and `best ask` values in the market order book during `periods * 2` periods of 1 second, to limit the price equally on both sides while sending new orders.

  * `OnTopsAPROff` - Same as `OnTops` when `apr` is `Off` or when the system is not aggressively rebalancing positions; otherwise if one side is rebalancing, is same as `Off` for that side.

  * `OnTop` - Use a quote protection STDEV, calculated from two sequences of the `best bid` (first sequence) and also of the `best ask` (second sequence) value in the market order book during `periods` periods of 1 second, to limit the price independently on each side while sending new orders.

  * `OnTopAPROff` - Same as `OnTop` when `apr` is `Off` or when the system is not aggressively rebalancing positions; otherwise if one side is rebalancing, is same as `Off` for that side.

* `periodsˢᵗᵈᶜᵛ` - Maximum amount of values collected in the sequences used to calculate the STDEV, each side may have its own STDEV calculation with the same amount of `periods`. After collect sequentially every 1 second the values of the `fair value`, `last bid` and also of the `last ask` from the market order book, and before place new orders, a limit will be always applied to the new orders price using a calculation of the STDEV, taking into account only the last `periods` periods in each sequence.

* `factor` - Multiplier used to increase or decrease the value of the selected `stdev` calculation, a `factor` of 1 does effectively nothing.

* `BB?` - Enable Bollinger Bands with upper and lower bands calculated from the result of the selected `stdev` above or below its own moving average of `periods`.

* `cxl?` - Enable a timeout of 5 minutes to cancel all orders that exist as open in the exchange (in case you found yourself with zombie orders in the exchange, because the API integration have bugs or because the connection is interrupted).

* `profit` - Timeframe in hours to calculate the display of Profit (under wallet values) and also interval in hour to remove data points from the Stats, for example a `profit` of 0.5 will compare the current wallet values and the values from half hour ago to display the +/- % of increment between both and will remove data from the Stats older than half an hour.

* `Kmemory` - Timeout in days for Pings (yet unmatched trades) and/or Pongs (K trades) to remain in memory, a value of `0` keeps the history in memory forever; a positive value remove only Pongs after `Kmemory` days; but a negative value remove both Pings and Pongs after `Kmemory` days (for example a value of `-2` will keep a history of trades no longer than 2 days without matter if Pings are not matched by Pongs; or a value of `-0.25` will do so but limited to 6h).

* `delayUI` - Relax the display of UI data by `delayUI` seconds. Set a value of 0 (zero) to display UI data in realtime, but this may penalize the communication with the exchange if you end up sending too much frequent UI data (like in low latency environments with super fast market data updates; at home is OK in realtime because the latency of **Krypto-trading-bot** with the exchange tends to be higher than the latency of **Krypto-trading-bot** with your browser).

* `audio?` - plays a sound for each new trade (ping-pong modes have 2 sounds for each type of trade).
