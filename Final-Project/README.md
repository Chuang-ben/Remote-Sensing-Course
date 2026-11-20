# 強降雨與颱風下的臺北市道路與避難所可達性分析

本專案結合臺北市道路路網、室內避難所、雨量與災害圖資，模擬強降雨對道路通行的影響。使用者可以在互動網站比較災前與災後路徑、查詢指定時間內可達的避難所；研究也以網格分析不同地區的道路韌性。

完整方法、圖表與研究討論見[期末專案報告](./期末專案.pdf)。

## 主要發現

- 以地勢與河川條件建立的淹水風險指標，與外部淹水資料比較後，報告採用 `total_flood_risk >= 3` 辨識潛在淹水風險網格。
- 在報告分析的 **2024-07-10 14:00** 強降雨情境中，部分網格的道路旅行時間增加約 7%；觀察到的封閉路段為隧道。
- 在全市相同雨量的模擬情境中，各類網格的平均旅行時間增加率約為 6.4%。平地高密度路網的累積延誤較高，但替代路徑與避難所可達性也較佳。

以上是特定資料與情境下的模擬結果，不代表即時道路狀況。

## 分析流程

| 步驟 | 檔案 | 內容 | 主要輸出 |
|---|---|---|---|
| 1. 災前資料 | `Final_01_pre.ipynb` | 建立道路路網、室內避難所配對與 250 m 網格；計算地形、河川及淹水風險。 | `output/taipei_drive.graphml`、`output/Taipei_grid_full_risk.geojson`、`output/taipei_road_grid_segments.geojson` |
| 2. 災後模擬 | `Final_02_post.ipynb` | 將測站雨量內插至網格，結合降雨、淹水與土石流規則，計算道路災前／災後旅行時間。 | `output/Taipei_grid_with_rain_post.geojson`、`output/road_grid_travel_time_pre_post.geojson` |
| 3. 區域比較 | `Final_03_user.ipynb` | 彙整網格旅行時間增加率，比較山區／平地及高／低路網密度。 | `output/Taipei_grid_travel_time_increase_rate_map.html`、`output/Taipei_density_terrain_comparison_summary.csv` |
| 4. 模型驗證 | `Final_04_validation.ipynb` | 以外部淹水圖層檢驗淹水風險指標，輸出混淆矩陣與門檻比較。 | `output/confusion_matrix_validation.png`、`output/flood_risk_validation_confusion_map.html` |

空間分析主要使用 `EPSG:3826`。淹水**風險指標**用於網格驗證；道路**通行模擬**則依雨量、淹水深度圖與土石流潛勢範圍調整車速或封閉道路。

## 開啟互動網站

以下指令以已建立的 `RSGI` Conda 環境為例。在專案根目錄執行：

```powershell
conda run -n RSGI python scripts\serve_route_comparison_web.py --port 8785
```

在瀏覽器開啟 <http://127.0.0.1:8785>。網站可：

1. 選擇歷史降雨情境，或自行輸入 1 小時、24 小時、2 日、3 日雨量。
2. 查看災前／災後道路、封閉路段與災害網格。
3. 指定起終點，比較最佳路徑與旅行時間。
4. 設定行車時間，查看災前／災後可達範圍與避難所。
5. 查看全市道路旅行時間與封閉統計。

目前歷史雨量資料涵蓋 **2024-04-18、2024-07-10、2024-07-24、2024-07-25**。若網站提示某個歷史時間點尚未建立情境，可先執行：

```powershell
conda run -n RSGI python scripts\build_road_scenario.py --target-time "2024-07-10 14:00:00"
```

再重新整理網站。自訂均雨量情境由網站建立。網站使用的情境資料位於 `Taipei_City_Urban_Resilience_Map_Website/`。

## 重新執行分析

依序執行 `Final_01_pre.ipynb`、`Final_02_post.ipynb`、`Final_03_user.ipynb`；`Final_04_validation.ipynb` 需先有第一步產生的風險網格。原始資料放在 `data/`，notebook 的結果寫入 `output/`。

`Final_02_post.ipynb` 目前設定 `RAIN_MODE = "mean"`，以 **20 / 100 / 150 / 200 mm** 作為四種累積雨量的均雨量模擬。若要分析測站雨量，將其改為 `"real"`，並檢查 notebook 中的雨量檔案與 `target_time`；目前預設為 `rain_20240710.csv` 與 `2024-07-10 14:00:00`。

本專案未附 Conda 環境檔或套件依賴清單；重跑前需備妥 notebook 與網站腳本使用的 Python 套件。`data/` 與 `output/` 也未納入 Git 追蹤，新環境需自行準備原始資料並產生輸出。

## 資料與專案目錄

| 路徑 | 用途 |
|---|---|
| `data/` | 雨量、淹水、土石流、數值地形、河川、行政界線與避難所原始資料。 |
| `output/` | notebook 產生的道路、網格、圖表與地圖。 |
| `scripts/` | 建立降雨情境、轉換 PMTiles 與啟動網站的腳本。 |
| `Taipei_City_Urban_Resilience_Map_Website/` | 網站頁面、PMTiles 圖層與各情境資料。 |
| `期末專案.pdf` | 研究方法、結果、圖表與完整參考文獻。 |

主要資料來源包括 [OpenStreetMap](https://www.openstreetmap.org/copyright)、[雨量觀測資料](https://data.gov.tw/dataset/9177)、[臺北市降雨積水模擬圖](https://data.gov.tw/dataset/121550)、[土石流潛勢溪流影響範圍](https://data.gov.tw/dataset/176526)及[避難收容處所](https://data.nat.gov.tw/dataset/77940)。其他資料來源與引用詳見期末專案報告。
