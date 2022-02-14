# pair-trade.

# version 1
This program is a pair trading model based on z-score. The specific idea is to find a cointegrated stock pair based on historical trading data, then determine whether the linear portfolio of the cointegrated stock pair deviates from the regression value in the short term based on z-score with different rolling windows, and then realize the arbitrage operation based on a pre-defined trading signal.

This model is based on the following basic assumptions:
1.	There are no transaction fees.
2.	There is no interest rate on short selling.
3.	The maximum amount of short selling is 1 million.
4.	Long selling is up to $1 million.
5.	There is no delisting risk.
6.	There is no dividends.

The specific process is as follows
1.	Set learning window and trading window (default: 3 months)
2.	Set the trading z-score threshold (default position opening threshold is 1, closing threshold is 0.1)
3.	Use the data in the learning window to find cointegration pairs in the target set (using p-value judgment)
4.	Calculate z-score (default: 1 month for long rolling window, 2 days for short rolling window)
5.	Long the portfolio when the threshold up crosses 1, or short the portfolio when the threshold down crosses -1
6.	When long, close the position when the combination z-score is down 0.1, and when short, close the position when the combination z-score is up 0.1
7.	No more than two trades within three months. If less than two trades are made within three months, the position will be forced to close on the end of the third month.
8.	Go back to Step 3 and use the new learning window to train the model to find the new cointegration pair.

The simulation results
In the given underlying portfolio, with the set limit of 1 million principal, a profit of 1,378,199 was achieved from January 1, 2018 to December 31, 2021, with an annual return of 24.2%.

Further discussion:
About the z-score
The strategy achieved profit in 13 out of 15 trading Windows in four years. Two of them were losses. There are two main reasons for its loss:
1.	To complete two trades within three months, the second trade is forced to close. 
2.	Z-score does not match the actual combination trend.

The first reason for the loss is the artificial safety measures, which have two purposes: one is to prevent the portfolio from deviating from the regression value for a long time at the end of the trading window, and the cointegration relationship is destroyed, which leads to the failure to return; the other is to prevent the portfolio from being unable to achieve profits for a long time, which increases the opportunity cost. If the learning window time is increased and the combination is sure to return based on other analysis, this condition can be deleted and final return is foreseeable. But opportunity costs still need to be considered.
The second reason for the loss is unavoidable. The advantages and disadvantages of using z-score are all reflected in the second point. Its disadvantage comes from the calculation principle of z-score. According to the cointegration theory, when we receive the trading signal, the portfolio returns with a high probability, but due to the uncertainty of the future, the portfolio still has a probability to continue to deviate from the regression value, and at this time, z-score may move in the opposite direction, to give the closing signal when the profit is not realized. z-score and portfolio trend mismatch is the root cause of the loss. On the other hand, this is also an advantage. Automatic stop loss is achieved to some extent with z-score. Since there is no artificial stop-loss line in the program, z-score's algorithm can automatically give a closing signal when the portfolio continues to deviate.

Next step:
1.	Constructing a new index to replace z-score requires the following characteristics: 1. Like z-score, it can reflect the position of the current data in the window. 2. The linear relationship with the combination is stronger, and the reverse movement should be avoided as far as possible.
2.	Increase the automation performance of the program, in the training window to train the transaction threshold instead of the using artificial transaction threshold.
3.	Increase transaction frequency and reduce opportunity cost.

# version 2
update：

Add two pictures on the end:

1.pictures of returens for each transaction cycle
2.pictures of accumulative returns from the first transaction cycle to the last

# version 3
update：

new assumption:

In version3, the principal is still one million, but in order to control the risk, the sum of the amount of short selling and the amount of long selling is one million. Margin for short selling is the amount of short selling, and when loss reaches the margin, position is forced to close out.

New transaction logic：

In version3, new transaction logic was adopted to increase transaction frequency. In version2, the training window and test window are fixed values determined by parameters. In the test set, when the number of transactions reaches the preset upper limit, the model adopts the strategy of waiting for the next window. In the new model, when the trading limit is reached in the test set, the next test set is immediately entered and the new training set is retraced forward. The new transaction logic effectively reduces opportunity cost.
At the same time, the method of finding cointegration pair is changed. Due to the adoption of the new transaction logic, cointegration pairs cannot be found in some training windows (p-value of all the underlying pairs is greater than 0.05). In order to find the new cointegration pair, the following adjustments are made in version3: when no cointegration pair appears, the training set is delayed one day and matched again until the new cointegration pair is found.

Although the benefits of version3 are lower than that of version2, version3 effectively reduces the system risk, makes full use of the time, avoids system hang-up, and effectively reduces the opportunity cost. Therefore, version3 can be considered as an improved version of version2.
