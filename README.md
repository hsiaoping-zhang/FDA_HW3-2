# FDA_HW3-2

## 資料集
 [[Link]](https://archive.ics.uci.edu/ml/datasets/Online+Shoppers+Purchasing+Intention+Dataset) Online Shoppers Purchasing IntentionDataset Data Set
 
 **Abstract**  
Of the 12,330 sessions in the dataset, 84.5% (10,422) were negative class samples that did not end with shopping, and the rest (1908) were positive class samples ending with shopping.



## 欄位介紹
- 不同類型的頁面被造訪
	- Administrative : `float` 行政網頁總數
	- Administrative_Duration : `float` 行政網頁瀏覽時間總數
	- Informational : `float` 資訊網頁總數
	- Informational_Duration : `float` 資訊網頁瀏覽時間總數
	- ProductRelated : `float` 產品網頁總數
	- ProductRelated_Duration : `float` 產品網頁瀏覽時間總數
- BounceRate `float`
	使用者進來的網頁，有多少比例的使用者會從這個網頁跳出而且沒有觸發其它網頁
- ExitRate `float`
	使用者瀏覽的最後網頁，這個在所有頁面中成為是最後一頁的比率
- PageValue `float`
	使用者完成 e-commerce transaction 的平均網頁瀏覽量
- SpecialTay : `float` 是否靠近特殊節日(比較被容易完成交易)
- OperatingSystems : `int` 使用的作業系統類型
- Browser : `int` 使用的瀏覽器類型
- Region : `int` 使用者所在區域
- TrafficType : `int`使用者的交通類型
- VisitorType : `string` returning / new visitor
- Weekend : `boolean` 是否為週末
- Month : `string` 月份
- Revenue : `boolean` 是否有購買

## 分析資料

**相關係數 Heatmap**

![](https://i.imgur.com/MzZYjxj.png=500x)

在顏色分布上，`BounceRates` (負相關)、`ExitRates` (負相關)、`PageValues` (正相關) 三者的數值相關係數對於 Revenue 比較明顯。

**購買與否欄位性質**

![](https://i.imgur.com/qj0xIhf.png=500x)

1. 頁面數值  
	瀏覽頁面的數量與時間對於是否購買的平均數值差別都可以達到 50%  
  
2. `SpecialDay` 欄位  
	一般認為在特別節日會貢獻較高的購買率，但從資料統計上來看，此網站的購買流量並非來自於特別節日(平均數值較高代表靠近特殊節日，但在平均值上，真正有購買的那些資料裡面，較少部份來自特殊節日因素)  
  
  
3. `OperatingSystems` 作業系統  
	作業系統在購買與不購買上，沒有顯著差異。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://i.imgur.com/UlruMLJ.png=500x)

	
4. `Browser` 瀏覽器種類  
	和作業系統一樣，沒有特別明顯的差異。
	
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://i.imgur.com/aUQ5ItG.png=500x)


5. `Month` 月份  
	月份的購買與否沒有特別哪個月份差異突出得以作為指標參考。
	
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://i.imgur.com/ZLjEQtF.png=500x)


從相關係數圖可以得知，Revenue 欄位和 PageValue 的相關程度較高，也能明顯得知 PageValue 是此資料集預測 Revenue 的重要欄位。但從另一個角度假設而言，此資料集屬於線上購物網站，則 Traffic Type (交通方式) 及 Region 居住區是否會是使用者偏好線上購物的指標之一？


## 定義問題

對於欄位 `Traffic Type` 和 `Region` 是否是此購物網站中，影響顧客是否消費的重要因素之一？

(假設對於某些特定比較不方便外出購物的交通方式 ex 步行，則使用者偏容易購物成功)

## 過程方法

### Original Result
和 Revenue 的相關係數

| 欄位         | 相關係數  |
| :-----------: | :---------: |
| Region       | -0.011595 |
| TrafficType | -0.005113 |


### Reason

在資料當中的 Traffic Type 和 Region 在 correlation 的顯示當中被當作為 `有序離散值`，因此在相關係數上表現不佳。

### Approach

1. 將 Revenue 為 True 和 False 的資料分開為 `buy_data` 和 `no_data`  

2. Traffic Type 和 Region 各別用 OneHotCoding 方式分成類別資料  

3. 比較 Revenue 不同，兩者在平均值上的差異，如下圖

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Traffic Type**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://i.imgur.com/emO5kYY.png=500x)

- `traffic_2` 欄位具有明顯差異(Buy 和 Non-Buy 差異大，且平均值高 ← 代表資料佔比不少)，可以當作指標
- 其它交通方式則相差沒有太多，因此額外作為指標

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Region**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://i.imgur.com/sf9525n.png=500x)

- 彼此之間的差異並沒到很多
- 最多 `region_1` 和 `region_3` 有相對來說較大的差異

4. 加入 correlation 進行相關係數比較

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://i.imgur.com/S6HNlbb.png=400x)


5. 比較有參考價值的欄位 : `traffic_2`

#### 小結
資料集當中，某一地區並不會特別影響購買率，但交通方式則是在 `traffic_2` 傾向購買率高。

### Improvment
將 traffic 分類的結果 (20 columns) 加到訓練資料，並移除 `TrafficType` 和 `Region` 欄位
- DecisionTree


|       類別       | Before | After  |
|:----------------:|:------:|:------:|
|   準確率最小值   | 0.8516 | 0.8586 |
|   準確率最大值   | 0.8545 | 0.8664 |
| 準確率**平均值** | 0.8528 | 0.8623 |

- RandomForest

|       類別       | Before | After  |
|:----------------:|:------:|:------:|
|   準確率最小值   | 0.8932 | 0.8895 |
|   準確率最大值   | 0.8944 | 0.8959 |
| 準確率**平均值** | 0.8938 | 0.8936 |
