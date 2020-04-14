作業三：Binomial Option Pricing Model Calculator
===
## 目錄
- [作業說明](#作業說明)
- [使用說明](#使用說明)
- [學習歷程](#學習歷程)
- [流程圖](#流程圖)

## 作業說明

A non-dividend-paying stock is selling for $160.

u = 1.5; d = 0.5; r = 18.232% per period

Hence p = (R − d)/(u − d) = 0.7.

Consider a European call on this stock with X = 150 and n = 3.

What is the call/put value? Or what is the PV of the expected payoff at expiration? (by backward induction).

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 使用說明

執行 [bopm.ipynb](https://github.com/manamimebom/Financial_Engineering/blob/master/HW3/bopm.ipynb) 檔案，依照指示分別輸入股票現價、上漲及下跌倍率、無風險利率（債券利率）、選擇權履約價及期數。

程式輸出最終的 Call/Put Value，並繪出 binomial process for the call/put price。

以下為範例結果：

```
Call value is 85.069.
Put value is 11.875.
```

<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW3/src/C_tree.png" width="150" >
</p>

<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW3/src/P_tree.png" width="150" >
</p>

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 學習歷程

老師上課提到有兩種算法，一種使用 Backward Induction，另外一種使用公式解。

這裡使用第一種方法。

首先如前段說明，輸入選擇權資訊，並計算出額外資訊如 Gross Return 與 Probability。

```省略輸入的部分```

```Python
R = round(math.exp(r), 2)
p = (R - d)/(u - d)
```

再求出經過一連串上漲或是下跌後、每一種可能的股票價格，依此價格算出最後可能的 Payoff。

```python
C_tree = np.zeros((n+1, n+1))
P_tree = np.zeros((n+1, n+1))


for j in range(n+1):
    C_tree[n, j] = round(max(S * (u**(n-j)) * (d**j) - X, 0), 3)
    P_tree[n, j] = round(max(X - S * (u**(n-j)) * (d**j), 0), 3)
```
依據這些 Payoff 一一回推每一期可能的 Payoff，回推至第零期時即為我們要求的 Call/Put Value。

```python
for i in reversed(range(n)):
    for j in range(i+1):
        C_tree[i, j] = round((p * C_tree[i+1, j] + (1-p) * C_tree[i+1, j+1]) / R, 3)
        P_tree[i, j] = round((p * P_tree[i+1, j] + (1-p) * P_tree[i+1, j+1]) / R, 3)

print('Call value is ' + str(C_tree[0,0]) + '.')
print('Put value is ' + str(P_tree[0,0]) + '.')
```

最後畫出 binomial process for the call/put price。

```python
x = []
y = []
s = []
for i in range(n+1):
    for j in range(i+1):
        x.append(i)
        y.append(j-i/2)
        s.append(C_tree[i, j])
# plt.figure(figsize=(16,16))
plt.figure(figsize=(8,8))
for i in range(n):
    for j in range(i+1):
        plt.plot([i, i+1], [j-i/2, j-(i+1)/2], alpha = 0.5)
        plt.plot([i, i+1], [j-i/2, j+1-(i+1)/2], alpha = 0.5)

for i in range(len(x)):
    plt.text(x[i]-0.1, y[i]-0.05, s[i], bbox=dict(facecolor='White', alpha=0.8, boxstyle='round, pad = 0.2, rounding_size = 0.2'), fontsize=20)
plt.axis('off')
plt.title('Call Value Tree', size = 20,loc = 'left')
# plt.savefig('/C_tree.png')
plt.show()
```



<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>

## 流程圖


<p align="center">
<img src="https://github.com/manamimebom/Financial_Engineering/blob/master/HW3/src/flowchart.png" width="150" >
</p>

<p align="right">
    <a href="#目錄" class="btn btn-primary">
        &uarr; 回到目錄
    </a>
</p>
