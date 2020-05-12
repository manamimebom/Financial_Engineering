作業五：Option Pricing with Hull White Model
===
## 目錄
- [作業說明](#作業說明)
- [使用說明](#使用說明)
- [學習歷程](#學習歷程)
- [流程圖](#流程圖)

## 作業說明

透過 Monte Carlo method，

對 Hull White Model 模擬 Short Rate。

將 Short Rate 帶入 Geometric Brownian Motion，r 換成 r(t) 模擬股價。

自訂選擇權履約價，對每一條 path 計算出到期日時的 PayOff，

對所有 Path 的 PayOff 進行期望值計算，並折現回 t=0 的時間點，

計算出 Call Price & Put Price。

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 使用說明

確定安裝完成 [QuantLib](https://www.quantlib.org)，執行 [option_pricing_HWmodel.ipynb](https://github.com/manamimebom/Financial_Engineering/blob/master/HW5/option_pricing_HWmodel.ipynb) 檔案，依照指示分別輸入股票現價、波動率、遠期利率、到期年限與期數、無風險報酬率、選擇權履約價及 Monte Carlo / Hull White Model 參數。

程式輸出最終的 Call/Put Option Value。

以下為範例與結果：

`Input`
> Current stock price: 100
> Volatility: 0.1
> Forward_rate (%): 5
> Maturing time (in year): 1
> Maturing period left: 360
> Strike Price: 105
> Riskless Rate of return (%): 1
> \# of Monte Carlo sample: 100
> a value for Hull White Model: 0.1

`Output`
<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW5/src/HW.png" width="500" >
</p>

<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW5/src/Price.png" width="500" >
</p>

>Put value is 110.767.
>
>Call value is 10.342.


<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 學習歷程

上次的作業為 BS Model 實作，其假設連續複利率為一致。

這次的課程主軸為 [Hull White Model](https://zh.wikipedia.org/wiki/赫爾懷特模型)，是一種將不確定的利率模型化，建立短期利率變動 (instantaneous short rate) 的過程，在風險中立世界下定義出目前的利率期間結構。 


<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW5/src/formula.png" width="500" >
</p>


首先如前段說明，輸入選擇權資訊後，透過 Monte Carlo method，使用 Hull White Model 模擬 Short Rate。

```python
def generate_paths(num_paths, timestep):
    arr = np.zeros((num_paths, timestep+1))
    for i in range(num_paths):
        sample_path = seq.next()
        path = sample_path.value()
        time = [path.time(j) for j in range(len(path))]
        value = [path[j] for j in range(len(path))]
        arr[i, :] = np.array(value)
    return np.array(time), arr
    
day_count = ql.Thirty360()
todays_date = ql.Date(12, 5, 2020)
ql.Settings.instance().evaluationDate = todays_date
spot_curve = ql.FlatForward(todays_date, ql.QuoteHandle(ql.SimpleQuote(forward_rate)), day_count)
spot_curve_handle = ql.YieldTermStructureHandle(spot_curve)

hw_process = ql.HullWhiteProcess(spot_curve_handle, a, sigma)
rng = ql.GaussianRandomSequenceGenerator(ql.UniformRandomSequenceGenerator(timestep, ql.UniformRandomGenerator()))
seq = ql.GaussianPathGenerator(hw_process, length, timestep, rng, False)

time, sr_paths = generate_paths(num_paths, timestep)

plt.figure(figsize = (6, 4), dpi = 200)
for i in range(num_paths):
    plt.plot(time, sr_paths[i, :], lw=0.8, alpha=0.6)
plt.title("Hull-White Short Rate Simulation")
plt.xlabel('Year')
plt.ylabel('Short rate')
plt.show()
```
將 Short Rate 帶入 Geometric Brownian Motion，r 換成 r(t) 模擬股價。

```python
def genBrownPath (T, mu, sigma, S0, dt):
    S = []
    W = [0] + np.random.standard_normal(size = 1) 
    W = (W + np.random.standard_normal(size = 1))*np.sqrt(dt)
    for i in range(len(time)):
        X_ = float(mu[i] - 0.5 * sigma**2) * float(time[i]) + sigma * W 
        S.append(S0 * np.exp(X_)) # == geometric brownian motion
    plt.plot(time, S)
    return S
    
p_paths = []
plt.figure(figsize = (6, 4), dpi = 200)
for i in range(num_paths):
    p_paths.append(genBrownPath(timestep, sr_paths[i, :], sigma, S0, dt))
plt.xlabel('Year')
plt.ylabel('Price')
plt.title('Stock Price Simulation')
plt.show()
```

自訂選擇權履約價，對每一條 path 計算出到期日時的 PayOff，

```python
# Output
call, put = [], []
for i in range(num_paths - 1):
    price = p_paths[i][timestep]
    if price - X > 0:
        payoff = price - X
        call.append(payoff + np.exp(-1.0*r*i)*payoff)
    if X - price > 0:
        payoff = X - price
        put.append(payoff + np.exp(-1.0*r*i)*payoff)
```

對所有 Path 的 PayOff 進行期望值計算，並折現回 t=0 的時間點，計算出 Call Price & Put Price。

```python
print('Put value is ' + str(round(np.mean(put), 3)) + '.')
print('Call value is ' + str(round(np.mean(call), 3)) + '.')
```
Code Reference: [Goutham Balaraman](http://gouthamanbalaraman.com/blog/hull-white-simulation-quantlib-python.html), [Pecu Tsai](https://colab.research.google.com/drive/1LL_m1UO_U2oHDMQhBDPjhUBANDpVhev7#scrollTo=NBA3FHuhlVh9).

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


