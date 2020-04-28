作業四：Black-Scholes-Merton Model Calculator
===
## 目錄
- [作業說明](#作業說明)
- [使用說明](#使用說明)
- [學習歷程](#學習歷程)
- [流程圖](#流程圖)

## 作業說明

A stock is currently priced at $75 and has a σ of 0.35.

It will pay two $1 dividends in 1 month and 4 months.

If r = 6%, what is the value of a European put and call option with an exercise price of $65 maturing in 6 months?

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 使用說明

執行 [bs_formula.ipynb](https://github.com/manamimebom/Financial_Engineering/blob/master/HW4/bs_formula.ipynb) 檔案，依照指示分別輸入股票現價、波動率、股利金額與給付次數、標的物報酬率、選擇權履約價及到期期長。

程式輸出最終的 Call/Put Option Value。

以下為範例與結果：

>Current stock price: 75
Volatility: 0.35
Times of dividends paid per year : 2
Dividend per payment: 1
Rate of return (%): 6
Strike Price: 65
Maturing time (in month): 6


>Put value is 2.86.
Call value is 12.806.

這裡預設股利皆為3個月發放一次，因此最多一年可以拿四次股利。


## 學習歷程

這次的作業主要是實作 Black-Scholes Model，並加入有股利之前提（即為 Black-Scholes-Merton Model）。

<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW4/src/bsmodel.png" width="150" >
</p>

首先如前段說明，輸入選擇權資訊，並依照 Black-Scholes-Merton Model 計算如下。

```python
ds = [d] * n
ds = [d * np.exp(-r*(i*3+1)/12) for i in range(n)]
D = np.sum(ds)

sh = S - D
d1 = (np.log(sh/X) + (r + 1/2 * v**2) * m) / (v * m**(1/2))
d2 = d1 - (v * m**(1/2))

n1 = 1/2 * (1 + math.erf(-1 * d1 / (2**0.5)))
n2 = 1/2 * (1 + math.erf(-1 * d2 / (2**0.5)))

put = X * np.exp(-r * m) * np_cal(-d2) - sh * np_cal(-d1)
call = sh * np_cal(d1) - X * np.exp(-r * m) * np_cal(d2)
```

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 流程圖


<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW4/src/flowchart.png" width="150" >
</p>

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

