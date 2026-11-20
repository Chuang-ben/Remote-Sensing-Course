# Remote Sensing Course｜ARIA 的演進與期末專案

這個 repository 收錄課堂練習、每週作業，以及期末專案。貫穿其中的問題是：**空間資料與遙測影像如何幫助我們辨識災害、檢查分析結果，並理解災後的可達性與韌性？**

前期從 API 資料、避難所點位與 GIS 分析出發；ARIA 系列逐步加入地形、降雨、路網、衛星影像、模型驗證與長期監測。期末專案則把其中的空間分析、雨量內插、路網與驗證方法，應用於臺北市強降雨下的道路通行與避難所可達性。

## 從資料可信度開始

[homework_01](homework_01/) 以 AQI API、地圖與距離計算練習資料取得和視覺化。[homework_02](homework_02/reflection.md) 審核避難所資料，指出「經緯度數值合理」仍需要行政區與實際地理位置檢驗。這個資料查核觀念延續到後面的風險建模與衛星影像驗證。

## ARIA 的演進

ARIA 從「避難所周邊有什麼風險」開始，逐步追問「雨來時道路還能不能走」、「衛星看到了什麼」，最後延伸到「災後環境如何恢復」。下表依作業順序整理每次新增的分析能力。

| 階段 | 核心問題與新增方法 | 作品 |
|---|---|---|
| v1｜河川與避難所 | 建立河川警戒緩衝區，空間連接避難所並分析收容量與風險分布。 | [homework_03 / ARIA](homework_03/ARIA.ipynb) |
| v2｜地形風險 | 聚焦花蓮縣，加入 DEM 高程、坡度與河川距離，評估避難所的多因子風險。 | [homework_04 / ARIA v2](homework_04/ARIA_v2.ipynb) |
| v3｜動態降雨 | 擴及花蓮與宜蘭，整合氣象署雨量 API、歷史事件與模擬情境，更新風險地圖。 | [homework_05 / ARIA v3](homework_05/ARIA_v3.ipynb) |
| 方法銜接｜雨量內插 | 比較最近鄰、IDW、Ordinary Kriging 與 Random Forest，並檢查預測不確定度。 | [homework_06 / Prediction Shootout](homework_06/Week6_Shootout.ipynb) |
| v4｜道路可達性 | 將雨量影響帶入花蓮道路路網，分析瓶頸、旅行時間、可達範圍與可能孤立的避難所。 | [homework_07 / ARIA v4](homework_07/ARIA_V4.ipynb) |
| v5｜三幕災害稽核 | 以馬太鞍溪事件的 Sentinel-2 事前、事件中、事後影像，辨識堰塞湖、崩塌與土石流並套疊受影響設施。 | [homework_08 / ARIA v5](homework_08/ARIA_v5_mataian.ipynb) |
| v6｜驗證與信心度 | 用雲遮罩、光譜指數差異、人工驗證點、門檻掃描與混淆矩陣，檢查災害偵測結果。 | [homework_09 / ARIA v6](homework_09/ARIA_v60.ipynb) |
| v7｜全天候觀測 | 加入 Sentinel-1 SAR 淹水偵測，與 Sentinel-2 光學結果比較，並以坡度減少地形誤判。 | [homework_10 / ARIA v7](homework_10/ARIA_v7.0.ipynb) |
| v8｜地表分類 | 用 K-means 與 Random Forest 分析震後地表覆蓋，並以混淆矩陣與外部圖層驗證。 | [homework_11 / ARIA v8](homework_11/ARIA_8.0.ipynb) |
| v9｜雲端時序 | 在 Google Earth Engine 分析太魯閣 NDVI 與 Sentinel-1 時序、震前震後合成影像。 | [homework_12 / ARIA v9](homework_12/ARIA_v9.0.ipynb) |
| v9.5｜韌性監測 | 串接 Landsat 長期 NDVI 趨勢、桃園埤塘水體變化與植生恢復比例，從單次事件延伸到長期變化。 | [homework_13 / ARIA v9.5](homework_13/ARIA_v9.5.ipynb) |

這段演進也改變了分析尺度：從單一避難所，到道路網路與災害範圍，再到跨年時序。每往前一步，都更需要對照外部資料、檢查不確定性，並說清楚模型能回答的問題。

## 期末專案：臺北市強降雨下的道路與避難所可達性

[期末專案](Final-Project/README.md)將研究範圍移到臺北市，分析降雨、淹水與土石流情境如何影響道路旅行時間、路徑選擇及避難所可達性。這是一個獨立的期末研究；完整論述與圖表見[期末報告](Final-Project/期末專案.pdf)。

| 步驟 | 內容 | Notebook |
|---|---|---|
| 災前基礎 | 建立 OpenStreetMap 行車路網、室內避難所配對與 250 m 網格；計算地形、河川及淹水風險。 | [Final_01_pre](Final-Project/Final_01_pre.ipynb) |
| 災後情境 | 將測站雨量內插至網格，結合降雨、淹水模擬圖與土石流潛勢區，估算道路災前／災後旅行時間。 | [Final_02_post](Final-Project/Final_02_post.ipynb) |
| 區域韌性 | 將道路旅行時間變化彙整到網格，比較地形與路網密度；網站另提供路徑及避難所可達性查詢。 | [Final_03_user](Final-Project/Final_03_user.ipynb) |
| 結果驗證 | 用外部淹水圖層檢驗網格風險指標，查看混淆矩陣與門檻表現。 | [Final_04_validation](Final-Project/Final_04_validation.ipynb) |

期末專案的互動網站可選擇歷史或自訂降雨情境，比較災前／災後道路、最佳路徑、指定時間內的可達範圍與附近避難所。網站啟動方式與資料需求記錄在[專案 README](Final-Project/README.md)。

## 課堂練習與閱讀順序

課堂練習與每週作業互相對照，前者介紹方法，後者把方法用在 ARIA：

| 課堂練習 | 對應方法 |
|---|---|
| [02](exercise_02/) · [03](exercise_03/) | API 資料、河川緩衝區與避難所。 |
| [04](exercise_04/) · [05](exercise_05/) · [06](exercise_06/) · [07](exercise_07/) | 向量與 DEM、動態地圖、空間內插、路網分析。 |
| [08](exercise_08/) · [09](exercise_09/) · [10](exercise_10/) · [11](exercise_11/) | 衛星影像、變遷驗證、SAR 與地表分類。 |
| [12](exercise_12/) · [13](exercise_13/) | Google Earth Engine 時序與長期韌性監測。 |

每週作業放在 homework_01 至 homework_13。建議依序閱讀：

1. [資料取得](homework_01/) → [避難所資料審核](homework_02/) → [ARIA v1](homework_03/) → [v2](homework_04/) → [v3](homework_05/)：建立可信的空間資料與風險指標。
2. [降雨內插](homework_06/) → [ARIA v4](homework_07/)：把降雨影響連到道路旅行時間與可達性。
3. [ARIA v5](homework_08/) → [v6](homework_09/) → [v7](homework_10/) → [v8](homework_11/)：從衛星影像辨識事件，加入驗證、SAR 與地表分類。
4. [ARIA v9](homework_12/) → [v9.5](homework_13/) → [期末專案](Final-Project/)：比較長期變化，並閱讀臺北市道路韌性研究。

各週使用的資料與執行環境不同；請以各資料夾的 README 或 notebook 說明為準。期末專案的原始資料與部分分析輸出未納入 Git，重跑方式詳見其 README。
