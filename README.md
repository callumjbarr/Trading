# SPY buy signal for NZ50 ETF Trade

After following markets for a few years, I’ve always wondered: Does the NZX just follow whatever happened overnight in the US market? It sure felt that way to me.

Technically, you’d test this with a regression, use a dummy variable to see if the NZX tends to go up when SPY does, and check its statistical significance.

But let’s be honest, that’s not as interesting. What I really wanted to know was: Can you make money off this? More specifically, could I trade in and out of the NZX50 and actually outperform the benchmark? They say you can’t time the market, but can I time the New Zealand market.

So, how to do test this? A back test in Python using Yahoo finance data. Why Yahoo? It’s free and easy to access using Python libraries like yfinance. 

Now, I needed something tradable that tracks the NZX50. I settled on Smartshares NZ Top 50 ETF (ticker: FNZ.NZ), mainly because it was on yahoo finance and had data going back to around 2010, which became the starting date of the backtest. 

In total I pulled data for SPY (as the signal), FNZ.NZ (as the traded ETF), and the NZX50 index (for benchmarking).

For the signal, I calculated the daily return of SPY. The original idea was simple: if SPY goes up, then buy the NZ ETF (FNZ.NZ) at the next NZ market open. Since the US market closes in the evening (New York time) and New Zealand is already into the next day, I shifted the SPY signal forward one day. This ensured I was using yesterday’s SPY return to decide whether to buy the NZ ETF today. 
The trading rules were simple:

•	If SPY’s return is greater than a set threshold, I buy (or continue holding) the NZ ETF at the market open.

•	If SPY’s return is below the threshold, I sell the NZ ETF at the open and move to cash.

The threshold was introduced to help reduce unnecessary trades. Since each trade includes a transaction cost, moving in and out of the market on tiny moves can eat into returns. By requiring SPY to be up by more than a small percentage, the strategy trades less frequently, ideally only when there’s a strong signal, which can lead to better net performance after fees.

From 2010 to the present day, the script loops through each trading day, either buying, selling, or holding the NZ ETF based on the signal. A transaction cost of 0.1% per trade was included to make the results more realistic by accounting for brokerage fees. I also imported the NZD/USD exchange rate so that all performance, including the SPY benchmark, could be compared in New Zealand dollars on each date.

To find the optimal threshold, I wrapped the script in a loop where the threshold value was adjusted. The backtest was then run from 2010 to today, testing a range of thresholds, from -5% to +5% in 0.1% increments. For each threshold, the script recorded the final portfolio value. The threshold that produced the highest return on the most recent day was selected as the optimal one. This ended up being -1%. I used an initial investment of $10,000 and carried out the analysis.

Now, the big question, did I time the market? The answer, yes, but it comes with some caveats. If your goal was to maximize returns, you would have been better off just investing in SPY and holding it the entire time. But if you're looking for New Zealand exposure, this strategy could offer a smarter way to approach it than simply buying and holding the NZX50.

Here’s how a $10,000 investment from 2010 to today would have played out:

•	SPY final value = $90,067

•	NZX50 = $36,656

•	Strategy = $52,921

<img width="865" height="588" alt="image" src="https://github.com/user-attachments/assets/3c655996-ae60-4d8f-9cbb-390b9a17228e" />

While the strategy did not outperform SPY, it did exceed the performance of the NZX50. But is this outperformance statistically significant? To investigate this, we first summarize the average monthly returns:

•	SPY = 1.25%

•	NZX50 = 0.75%

•	Strategy = 0.94%

We test whether the strategy's mean return is statistically higher than the NZX50’s using a one-sided paired t-test. The hypotheses are:

•	H0 (Null Hypothesis): Strategy return <= NZX50 return. 

•	HA (Alternative Hypothesis): Strategy return is > NZX50 return.

The t-test yields a p-value of 0.0267. Since this is below the 5% significance level, we reject the null hypothesis. This provides evidence, at the 95% confidence level, that the strategy’s average monthly return is statistically greater than that of the NZX50 index.
