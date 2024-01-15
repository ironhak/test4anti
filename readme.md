# Classifying Candles project
The attempt is to find a way to classify the **current** market situation, not to predict it. Classifying the market situation is useful in order to have an *objective* way of following it, also it gives you a map of what's probable; the paradox is that everything is possible on every moment, but still knowing what is most likely, assuming normal conditions, is certainly of help. 

Even tough this model does not predict, and it would be impossible for it to do so because of the nature of the model itself (all the statistics are collected on post-fact candlesticks), you can infer on the price current process based on the outcomes of a large sample size. 

## Project outline
| <center><nobr>Model version</center> | <center>Details</center> |
|---------------|---------|
|[Model v.1](./classify_candles%20v1.ipynb) |   <ul><li>Creation of a rolling time frame based on tick charts.<li> Collected informations about High/Low time of creation<li> Created a classification system based on average and st.dev of the Open-Close distance.</ul>|
|[Model v.2](./classify_candles%20v2.ipynb) | <ul><li>Added wick sizes measurements.<li> Changed classification system based on the average directional candlestick, instead of using st.dev. it has been chosen the avg. wick size because it's more coherent with the whole logic.</ul>|
|[Model v.3](./classify_candles%20v3.ipynb) |<ul><li>It has been created a code that allows to rapidly print all the relevant statistics of a given price-frame.<li> Added statistics relative to<ul><li>% of times the given category happened on the dataset.<li>Average wick size.<li>Average wicks creation time.</ul><li>Expanded categorization based on the creation order of High/Lows.</ul> |
|[Model v.4](./classify_candles%20v4.ipynb) |<ul><li>*Improvements of the code*: each function has been generalized, and it can be used to create multiple instances.<li> Added statistics relative to the smaller price-frame (i.e. 25%).</ul> |

## Main concepts
### 1. Implications of the Open-Close distance
The main properties of a bullish candle, that can be understood from the average bullish candle, are:
1. If you don't care about the high/low order, overall you'll see the low is built before the high.

2. In these candles the high is built around the (calculation period x 0.75)/(calculation period)th tick, i.e. ticktime-wise at about 25 % of the candlestick period before that candlestick closes bullish. 
    >*Maybe this can be called a "market constant", basically you can choose ANY price-frame size and without doing any statistics you can immediately know, on average, when a High/Low will be created in the time-axis.*
3. The wicks become shorter the stronger the candlestick's strength (close-open distance) is. 
    >*In other words: the higher OC the lower DD of the trend (always remember, these are ex-post considerations!).*

Consequence of the points above are the following considerations about the Open-Close range:
- If OC is smaller than the average candlestick's OC range (regardless of the high/low order), we can conclude that current performance is weak.
- If the candlestick closes in the area between High-Close of the average bullish candlestick (regardless its order), we know that we're in a zone that statistically an average candlestick should visit during its lifetime. Thus, it's a medium move (i.e. nothing out of the ordinary).
- If a bullish candlestick closes above the High of the average bullish candlestick (regardless of its high/low order), we know it's performing better than most bullish candlesticks.

Thus, the last 2 possibilities indicate a trend, and we could hold on to our long trade (if we're in a long position).

### 2. Smaller price-frames
Regardless of the Open-Close range, a counter trend can **always** start, that is why the second most important measure to look at is the High-Close distance (vice-versa for downtrends); it's basically the draw-dawn of the current trend. 

**Problem:** How do you know if the High-Close (countertrend) distance you're seeing is being created right now or if it was created too long ago, maybe at the start of the candlestick? You certainly don't want to manage a trade with informations that do not represent current situation. 

**Solution:** On previous section we understood that High will be created at 75% of the lifetime in a bullish candle, thus the remaining $25\%$ should represent the final retracement of a bullish candle. The logical choice would be to create a price-frame with a period/tick/dimension $25\%$ of the main one, that way you can measure in real-time the High-Close measure. 

- Now imagine you opened a long trade because Open-Close distance on main price-frame was relevant, you need to start looking at the smaller price-frame in order to see the counter-trend force.
- If the Open-Close range of the $25\%$ price-frame becomes smaller than the average performance of a bearish candlestick, we could think about exiting or managing/securing our long trade. 
- If it closes below the average low of that $25\%$ candlestick, we should definitively exit. Why? Because according to these findings we now have a much higher chance that this low will be expanded to the downside.


> According to the square time principle from random walk theory, if you're looking at two time frames that are in a 4:1 ratio, the average candlestick's ranges of the smaller candlestick will be 1/2 of that of the higher TF.

Indeed, there are several connections between the $1000$ ticks price-frame and the $0.25\cdot1000$ price frame, here's some data:


<div style="float:left;"><div style="margin-right:20px;"><p style="font-size: 24px;">1000 tick dimension</p><hr><hr><p style="color:green; font-size: 24px;">Bullish</p><hr><p style="color:green; font-size: 16px;">Irrespective of High being before or after Low</p><table>
<thead>
<tr><th style="text-align: right;">                           Bullish</th><th style="text-align: right;">    All</th><th style="text-align: right;">   Weak</th><th style="text-align: right;">  Medium</th><th style="text-align: right;">  Strong</th></tr>
</thead>
<tbody>
<tr><td style="text-align: right;">                           percent</td><td style="text-align: right;">    49%</td><td style="text-align: right;">    29%</td><td style="text-align: right;">      8%</td><td style="text-align: right;">     11%</td></tr>
<tr><td style="text-align: right;">                      average body</td><td style="text-align: right;">0.00046</td><td style="text-align: right;">0.00021</td><td style="text-align: right;"> 0.00056</td><td style="text-align: right;"> 0.00105</td></tr>
<tr><td style="text-align: right;">                average upper wick</td><td style="text-align: right;">0.00021</td><td style="text-align: right;">0.00024</td><td style="text-align: right;"> 0.00018</td><td style="text-align: right;"> 0.00016</td></tr>
<tr><td style="text-align: right;">                average lower wick</td><td style="text-align: right;">0.00021</td><td style="text-align: right;">0.00024</td><td style="text-align: right;"> 0.00018</td><td style="text-align: right;"> 0.00016</td></tr>
<tr><td style="text-align: right;">           average upper wick time</td><td style="text-align: right;">    729</td><td style="text-align: right;">    651</td><td style="text-align: right;">     810</td><td style="text-align: right;">     878</td></tr>
<tr><td style="text-align: right;">average upper wick time percentage</td><td style="text-align: right;">    72%</td><td style="text-align: right;">    65%</td><td style="text-align: right;">     81%</td><td style="text-align: right;">     87%</td></tr>
<tr><td style="text-align: right;">           average lower wick time</td><td style="text-align: right;">    242</td><td style="text-align: right;">    310</td><td style="text-align: right;">     165</td><td style="text-align: right;">     119</td></tr>
<tr><td style="text-align: right;">average lower wick time percentage</td><td style="text-align: right;">    24%</td><td style="text-align: right;">    31%</td><td style="text-align: right;">     16%</td><td style="text-align: right;">     11%</td></tr>
</tbody>
</table><hr></div></div><div style="float:left;"><div style="margin-right:20px;"><p style="font-size: 24px;">250 tick dimension</p><hr><hr><p style="color:green; font-size: 24px;">Bullish</p><hr><p style="color:green; font-size: 16px;">Irrespective of High being before or after Low</p><table>
<thead>
<tr><th style="text-align: right;">                           Bullish</th><th style="text-align: right;">    All</th><th style="text-align: right;">   Weak</th><th style="text-align: right;">  Medium</th><th style="text-align: right;">  Strong</th></tr>
</thead>
<tbody>
<tr><td style="text-align: right;">                           percent</td><td style="text-align: right;">    49%</td><td style="text-align: right;">    30%</td><td style="text-align: right;">      7%</td><td style="text-align: right;">     11%</td></tr>
<tr><td style="text-align: right;">                      average body</td><td style="text-align: right;">0.00023</td><td style="text-align: right;">0.00011</td><td style="text-align: right;"> 0.00028</td><td style="text-align: right;"> 0.00052</td></tr>
<tr><td style="text-align: right;">                average upper wick</td><td style="text-align: right;"> 0.0001</td><td style="text-align: right;">0.00011</td><td style="text-align: right;">   8e-05</td><td style="text-align: right;">   7e-05</td></tr>
<tr><td style="text-align: right;">                average lower wick</td><td style="text-align: right;">  9e-05</td><td style="text-align: right;">0.00011</td><td style="text-align: right;">   8e-05</td><td style="text-align: right;">   7e-05</td></tr>
<tr><td style="text-align: right;">           average upper wick time</td><td style="text-align: right;">    181</td><td style="text-align: right;">    162</td><td style="text-align: right;">     200</td><td style="text-align: right;">     216</td></tr>
<tr><td style="text-align: right;">average upper wick time percentage</td><td style="text-align: right;">    72%</td><td style="text-align: right;">    65%</td><td style="text-align: right;">     80%</td><td style="text-align: right;">     86%</td></tr>
<tr><td style="text-align: right;">           average lower wick time</td><td style="text-align: right;">     57</td><td style="text-align: right;">     72</td><td style="text-align: right;">      41</td><td style="text-align: right;">      27</td></tr>
<tr><td style="text-align: right;">average lower wick time percentage</td><td style="text-align: right;">    22%</td><td style="text-align: right;">    29%</td><td style="text-align: right;">     16%</td><td style="text-align: right;">     11%</td></tr>
</tbody>
</table><hr></div></div><div style="clear:both;"></div>










<!-- 


#### $36$ combinations
It has now become clear how each trend can be subdivided in $6$ categories: weak-medium-strong for both bullish and bearish. Taking into account also the lower dimension, now we have $6^2=36$ possible configurations.

![](https://www.forexfactory.com/attachment/image/839624?d=1322097683)


#### $6^3=216$ combinations 
You could also measure the counter-trend potential of the counter-trend, this would be the $25\%$ dimension of the lower dimension... In other words, this can introduce:
- View the possibility of price turning back in your favor during a trade which is in drawdown or that is doing a retest. 
    > Usually most of the traders just wait and hold the position hoping for a trade to stop being in drawdown and start going into their favor, others just cut the loss as soon as possible; with this measure you could *objectively* know if it's worth it to wait before closing or if it's time to get out.

- The possibility of price reverting against your trade when also the lower dimension is in our favor (price is making new highs), in other words: a fastest way to see potential reversion of price.

> This subsection tough is really more of a future idea... Mastering $36$ possible configurations should keep busy for a while. 
-->

