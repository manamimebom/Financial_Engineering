作業一：本金平均攤還試算
===

[TOC]

## 作業說明
完成本金平均攤還試算，參考：https://ttc.scu.org.tw/memdca1.htm
## 使用說明

執行 ``CAM.ipynb`` 檔案，依照指示分別輸入本金、期數（年）、利率。
程式輸出 csv 檔，檔名為``本金平均攤還試算利息.csv``，即可查閱。
檔案如下圖：

![本金平均攤還試算利息.csv](https://i.imgur.com/Jl37vPq.png=10x)

若要用 Excel 打開 csv 檔需注意格式問題，可按照[此頁步驟](https://https://www.managertoday.com.tw/articles/view/55615)選擇適當的編碼與分隔方式檢閱。


## 學習歷程

首先把簡單輸入跟計算的部分完成。在這邊遇到的問題是，不太確定參考網頁的小數如何進位，導致最後的金額有些許出入，不過稍微觀察一下就可以解決了。

```python=
payment_i = []
payment_total = []
for t in range(period):
    if payment_p[t] > principal-payment_p[t]:
        payment_p.append(int(principal-payment_p[t]))
    else: 
        payment_p.append(payment_p[0])
    payment_i.append(round(principal * interest / 12))
    principal -= payment_p[t]
    if t == 0:
        payment_total.append(payment_p[t]+payment_i[t])
    else:
        payment_total.append(payment_total[t-1]+payment_p[t]+payment_i[t])
```

再來解決輸入時的例外情形。有時候使用者可能會輸錯數字，導致程式運行時出現錯誤，因此在運算之前就先把例外情形都排除掉。
這邊有個偷吃步，其實觀察一下參考網頁的程式碼，就能找到他們大概排除了哪些例外情形。
以下示範輸入本金的部分：

```python=
principal = input('請輸入本金（萬元）:')
while True:
    try:
        principal = float(principal)*10000
        if principal < 0:
            principal = input('請輸入正確的本金金額數目：')
            continue
        break
    except ValueError:
        principal = input('請輸入正確的本金金額數目：')
```

最後是輸出的部分。本來想要直接在程式執行頁面輸出結果，考慮到排版及閱讀難易度的問題，最後選擇輸出成 csv 檔。

```python=
import csv
with open('本金平均攤還試算利息.csv', 'w', newline='',encoding='utf-8') as csvfile:
    writer = csv.writer(csvfile)
    writer.writerows(table1)
```

## 流程圖


![](https://i.imgur.com/NIKZC3S.png)
