作業二：債券殖利率、即期利率與遠期利率
===
## 大綱
- [作業說明](#作業說明)
- [使用說明](#使用說明)
- [學習歷程](#學習歷程)
- [流程圖](#流程圖)

## 作業說明

計算市場債券報價中的 YTM、Spot Rate、Forward Rate 以及建立 Forward Rate 對照表

## 使用說明

執行 [spot_rate_and_foward_rate.ipynb](https://github.com/manamimebom/Financial_Engineering/blob/master/HW2/spot_rate_and_foward_rate.ipynb) 檔案，依照指示分別輸入債券合約資訊與其他期數債券的即期利率。

程式輸出 csv 檔，檔名為``foward_rate.csv``，即可查閱。

範例檔案如下圖，輸入參考綠角財經筆記：

<p align="center">
<img src="https://i.imgur.com/r84jhYM.png" width="350" >
</p>

若要用 Excel 打開 csv 檔需注意格式問題，可按照[此頁步驟](https://www.managertoday.com.tw/articles/view/55615)選擇適當的編碼與分隔方式檢閱。

## 學習歷程

第一步先算出債券的殖利率。有兩種算法，一種是逼近後求近似值，另外一種是上課提到的最佳化。

這裡使用第一種方法，從 coupon rate 開始，嘗試求出債券殖利率。

參考網站：[Bond Yeild Calculator](https://www.calkoo.com/en/ytm-calculator)

```python
def PV_cal(F, C, n, m, r):
    return C * (1 - (1 + r/m)**(-n*m)) / (r/m) + F / (1 + r/m)**(n*m)

ytm = CouponRate
period = int(Year*PaymentCnt)
if ytm == 0.0:
    ytm += 1e-8
while 1:
    pv = PV_cal(Par, Coupon, Year, PaymentCnt, ytm)
    if abs(Price - pv) <= 0.001:
        break
    if Price < pv:
        ytm += 0.0000001
    else:
        ytm -= 0.0000001
```

求出殖利率之後，下一步是要求出即期利率。在這邊卡了非常久，原因是在閱讀綠角的說明時，一直沒有意識到其他期的即期利率並不能從 input 的這支債券求出來，而是要另外輸入，才能計算下一步的遠期利率。

但總而言之，這部分是拆掉 coupon bearing bond 再計算即期利率。計算的方式是把每次的現金流想成一支獨立的零息債券，由現金流的 present value 和 future value 計算出即期利率（殖利率）。

如果是 zero-coupon bond，殖利率本身就是即期利率了。

參考網站：[綠角財經筆記：如何由債券殖利率算出理論即期利率](http://greenhornfinancefootnote.blogspot.com/2010/06/how-to-compute-theoretical-spot-rates.html)

```python
def sr_cal(P, F, C, n, m, r):
    if C == 0:
        sr = (P/F)**(-1/n)-1
        return sr
    pv = (F+C)/(1+r)**n
    pv = pv/(F+C)
    sr = pv**(-1/n)-1
    sr = sr * m
    return sr


spot_rate = sr_cal(Price, Par, Coupon, period, PaymentCnt, ytm)
```

第三部分是計算遠期利率。就像前面說的，需要先輸入其他期的即期利率。有了每一期的即期利率，就能算出不同期限的遠期利率了。

計算的方式參考講義公式如下：

<p align="center">
<img src="https://i.imgur.com/0nxwrq6.png" width="350" >
</p>

參考網站：[綠角財經筆記：如何由理論即期利率算出遠期利率](http://greenhornfinancefootnote.blogspot.com/2010/08/how-to-compute-forward-rates-from.html)

```python
def fr_cal(i, j, sr):
    if i == j:
        return 0
    sr_i = (1+sr[i])**(i+1)
    sr_j = (1+sr[j])**(j+1)
    fr = (sr_j/sr_i)**(1/(j-i))-1
    return fr

fr = np.zeros((period, period))
for i in range(period):
    for j in range(i, period):
        fr[i, j] = fr_cal(i, j, sr_list)
```

最後是輸出的部分，選擇輸出成 csv 檔。

```python
import csv
with open('foward_rate.csv', 'w', newline='', encoding='utf-8') as csvfile:
    writer = csv.writer(csvfile)
    writer.writerows(ans)
```

## 流程圖


<p align="center">
<img src="https://i.imgur.com/pE1OYLz.png" width="150" >
</p>

