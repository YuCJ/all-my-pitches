### 新聞的資料處理技能

---

## 資料處理的任務：

- 資料品質評估（正確性）
- 清理資料
- 分析資料
- 呈現資料

---

### 資料品質評估

- 資料建立的**方式**是否符合你要的目標？（EX：？）
- 資料**建立者的可靠度**？（EX：旺中集團作的統獨民調？）
- Describe 資料，數據和你對現象知識之間是否有落差？
- 問該領域的專家，外人可能不知道的資料品質的眉角。（EX：空污資料、人口資料）

---

### 案例1：2018村里長選舉

- 資料：中選會村里長登記參選PDF檔
- 要回答的問題：全國哪個村里參選人最多？平均每個里多少人參選？
- [資料檔案下載](https://drive.google.com/drive/folders/1JBLUSyGRldgSNtn0gzpQj83BbKWsPaoz)

+++

#### Tableau Hint

- Calculate Field
  - [縣市] = `LEFT([選舉區],3)`
  - [鄉鎮市區村里] = `RIGHT([選舉區], LEN([選舉區]) - 3)`
  - [西元登記日期] = `MAKEDATE(INT(SPLIT([登記日期],'/', 1)) + 1911, INT(SPLIT([登記日期],'/', 2), INT(SPLIT([登記日期],'/', 3))))`

+++

#### Tableau Hint

- Aggregate Measures
  - 該類別所有 records 數 / 該類別的村里數 = 平均每個村里有幾個參選人：`SUM([Number of Records]) / COUNTD([鄉鎮市區村里])`

---

### 案例2：租屋市場

- 資料：
  - 租屋實價登錄（X）
  - [591出租成交行情](https://www.591.com.tw/webService-market.html)（O）
- 要回答的問題：台灣近年租屋市場的變化趨勢是什麼？
- [資料檔案下載](https://drive.google.com/drive/folders/1T_YEKm4N3TkeizoZM6_htWo5xMOkYPPG?usp=sharing)
- https://www.twreporter.org/i/rent-house-in-difficulty-generation-gcs

---

### 案例3：租稅優惠

- 資料：
  - 促產等租稅優惠政策補助公司清單（X）
  - [公開資料觀測站](http://mops.twse.com.tw/mops/web/index)上市櫃公司財報（O）
- 要回答的問題：台灣哪些公司得到了多少減稅優惠的補助？
- [資料檔案下載](https://drive.google.com/drive/folders/1hvlQgi3ku1V2_iD90WHUkVNzso_SYsVf?usp=sharing)

---

### 案例4：高教資源分配不平等

- 資料：
  - 已經資料整理、分析完畢的論文。從報稅資料整理出不同收入和資產的家庭子女獲得高教資源的結果不均等的現象。
- 要進行的處理：怎麼互動化？視覺化？
- [程式碼](https://github.com/twreporter/infographic-education-opportunity-inequality)

---

### 要做的事情：

- 列出你想要回答的問題是什麼
- 你蒐集到的數據品質如何，可以回答你想回答的問題嗎？
- 你蒐集到的數據有沒有呈現什麼你沒想到的趨勢或現象？
- 如果現在手上的資料不夠或無法處理，你有什麼其它替代方案可以回答你的問題？