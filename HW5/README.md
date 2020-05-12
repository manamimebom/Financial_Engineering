作業五：Option Pricing with Hull White Model
===
## 目錄
- [作業說明](#作業說明)
- [使用說明](#使用說明)
- [學習歷程](#學習歷程)
- [流程圖](#流程圖)

## 作業說明

透過 Monte Carlo method
對 Hull White Model 模擬 Short Rate
將 Short Rate 帶入 Geometric Brownian Motion，r 換成 r(t) 模擬股價
自訂選擇權履約價，對每一條 path 計算出到期日時的 PayOff
對所有 Path 的 PayOff 進行期望值計算，並折現回 t=0 的時間點
計算出 Call Price & Put Price

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 使用說明

執行 [bs_formula.ipynb](https://github.com/manamimebom/Financial_Engineering/blob/master/HW5/option_pricing_Hull_White.ipynb) 檔案，依照指示分別輸入變數（股票現價、波動率、股利金額與給付次數、標的物報酬率、選擇權履約價及到期期長）。

程式輸出最終的 Call/Put Option Value。

以下為範例與結果：

> Current stock price: 75
>
>Volatility: 0.35
>
>Times of dividends paid per year : 2
>
>Dividend per payment: 1
>
>Rate of return (%): 6
>
>Strike Price: 65
>
>Maturing time (in month): 6


>Put value is 2.86.
>
>Call value is 12.806.

這裡預設股利皆為3個月發放一次，因此最多一年可以拿四次股利。

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 學習歷程

```
這次的作業主要是實作 Black-Scholes Model，並加入有股利之前提（即為 Black-Scholes-Merton Model）。

此為 Black-Scholes Formula。

<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW4/src/bsmodel.png" width="500" >
</p>

要計算 Black-Scholes-Merton Model 的話，須將 S 替算成 S_hidden = S - D，D 為股利現值總和。

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
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW5/src/flowchart.png" width="150" >
</p>

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

