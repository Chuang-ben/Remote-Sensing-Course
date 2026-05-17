# ARIA v8.0 Post-Earthquake Land Cover Classification Report

## 1. Abstract

本研究以 2024 年 4 月 3 日花蓮地震後的 Sentinel-2 L2A 影像，針對秀林鄉/太魯閣周邊進行災後土地覆蓋分類。研究使用 6 個多光譜波段與 SCL 雲遮罩進行前處理，並比較 K-means 非監督分類與 Random Forest 監督式分類。Random Forest 測試準確率為 0.8298，OOB accuracy 為 0.8344。外部驗證則使用 SWCB 官方新生崩塌地資料檢查 Bare/Landslide 類別，結果顯示 IoU 偏低，反映裸地分類與官方崩塌地定義之間存在差異。

## 2. Study Area and Data

研究區為秀林鄉/太魯閣周邊，範圍設定為 `TAROKO_BBOX = [121.40, 24.10, 121.80, 24.25]`。此範圍涵蓋中央山脈東側山區、河谷、蘇花公路沿線與太平洋近海區域。

本研究使用 2024 年 8 月 27 日 Sentinel-2 L2A 震後影像，影像雲量約 8.4%。使用波段包含 `B02`, `B03`, `B04`, `B08`, `B11`, `B12`，並使用 `SCL` 進行雲、雲影與無效像素遮罩。

## 3. Preprocessing

影像前處理包含三個步驟。首先將 Sentinel-2 影像數值除以 10000，轉換為 surface reflectance。其次，使用 SCL 只保留有效地表像素，包括 vegetation、not vegetated 與 water。最後，將 6 個波段整理成像素層級的特徵矩陣 `X_valid`，供 K-means 與 Random Forest 使用。

經前處理後，無效像素如雲、雲影與部分缺值區域被遮罩，因此分類圖中的白色區域主要代表未納入分類的像素。

## 4. Task 1: K-means Unsupervised Classification

K-means 分類設定為 `K = 5`，將研究區依光譜特徵分成五個 cluster。此方法不使用訓練樣本，因此適合作為初步探索土地覆蓋光譜差異的方法。

從平均光譜曲線觀察，水體類型最容易判讀，因為其在 NIR 與 SWIR 波段反射率明顯偏低，與其他地物差異清楚。森林類別也相對容易辨識，通常具有較高的 B08 反射率，以及較低的可見光紅光反射率。

較難解讀的是裸地、農地與建成區之間的群集。這些地物在可見光與 SWIR 波段可能具有相似反射特徵，尤其在山區環境中，裸露河床、道路、崩塌地、乾燥農地與建成區容易混合。平均光譜圖也顯示 B11 對區分不同 cluster 具有重要作用，因為裸地、岩石、植被與水體在 SWIR1 波段的差異較明顯。

## 5. Task 2: Random Forest Supervised Classification

Random Forest 使用 Google Earth 標記的五類訓練樣本進行分類，包括 Water、Forest、Cropland、Bare 與 Built-up。每一類皆以多個 polygon 作為訓練區域，並轉換成像素樣本。模型共使用 4756 個訓練像素，每個像素包含 6 個 Sentinel-2 波段特徵。

模型訓練結果顯示，Training accuracy 為 1.000，Test accuracy 為 0.8298，OOB accuracy 為 0.8344。OOB 與測試準確率接近，表示模型整體表現穩定；但訓練準確率明顯較高，也顯示模型可能對訓練樣本有一定程度的過擬合。

Feature importance 結果顯示 B11（SWIR1）為最重要波段。這是合理的，因為 SWIR1 對地表水分含量、裸露土壤與岩石表面非常敏感。秀林/太魯閣研究區包含森林、河床、水體、裸地與崩塌地，這些地物在 B11 上具有明顯差異。因此，B11 能有效協助模型區分主要地物類別。

## 6. Task 3A: Internal Accuracy Assessment

內部驗證結果顯示，Random Forest 的 Test accuracy 為 0.8298，OOB accuracy 為 0.8344，兩者差距很小，表示 OOB 驗證與 20% hold-out test set 評估結果一致。

分類表現中，Water 的 precision、recall 與 F1-score 皆為 1.00，效果最好；Forest 的 F1-score 為 0.87，Cropland 為 0.80，也有不錯表現。相較之下，Bare 的 F1-score 為 0.68，Built-up 僅為 0.30，表示這兩類較容易與其他高反射地表混淆。

Macro avg F1 為 0.73，Weighted avg F1 為 0.82，差距為 0.09，大於 0.03。這代表分類結果受到類別不平衡與少數類別表現較差的影響。Built-up 的 support 僅有 65，且 recall 只有 0.23，因此其分類可靠度相對較低。

## 7. Task 3B: SWCB Landslide Validation

外部驗證使用 SWCB 官方新生崩塌地資料，並將 RF 的 Bare 類別視為可能崩塌/裸地區域進行比較。結果顯示，Recall 為 0.213，Precision 為 0.002，IoU 為 0.002。

Recall 表示官方崩塌地中約 21.3% 被 RF 偵測為 Bare。Precision 與 IoU 很低，代表 RF Bare 類別包含大量 SWCB 未標示的新生崩塌地以外區域。主要原因是兩者類別定義不同：RF Bare 類別包含河床、裸露土壤、道路邊坡、海岸裸地、岩壁與可能的崩塌地，而 SWCB 只包含官方判釋的新生崩塌地。

從 overlay 圖觀察，FN 主要集中在研究區東側靠近太魯閣/立霧溪下游附近，以及山區零星狹長崩塌位置。這些區域可能受到 Sentinel-2 20 m 解析度限制、混合像素、坡面陰影、植被遮蔽或雲遮罩影響，導致 RF 未能完整偵測。

## 8. Task 4: AI Classification Report

面積統計結果顯示，Forest 面積約 22,797.6 公頃，占 36.1%；Water 面積約 22,557.7 公頃，占 35.8%；Cropland 面積約 7,027.6 公頃，占 11.1%；Bare/Landslide 面積約 8,828.6 公頃，占 14.0%；Built-up 面積約 1,873.0 公頃，占 3.0%。

LLM 產生的報告中，面積數字與 class area statistics 表格一致，未發現明顯數字幻覺。報告也正確引用 Test accuracy 約 83.0% 與 SWCB IoU 約 0.002。不確定性描述合理，因為報告指出 RF Bare 類別與 SWCB 官方新生崩塌地資料定義不同，也提到 Sentinel-2 20 m 解析度、混合像素、雲與陰影遮蔽，以及影像日期與 SWCB 判釋日期不同等因素。

若要改進報告，可更明確說明 Precision 僅 0.002，代表 RF Bare 類別產生大量 false positives，不應直接等同於官方崩塌地。此外，也可補充 FN 主要集中於山區小型或狹長崩塌地的可能原因。

## 9. ARIA v8.0 Upgrade Reflection

相較於前幾週以 NDVI、SAR 或固定閾值進行判釋的方法，ARIA v8.0 的分類器方法能同時使用多個光譜波段，並學習不同地物之間更複雜的光譜差異。

K-means 提供不需要訓練資料的探索方式，適合初步觀察研究區光譜群集。Random Forest 則能利用人工標記樣本建立較具解釋性的土地覆蓋圖，並提供 feature importance、accuracy assessment 與 confusion matrix 等評估工具。

然而，分類器的表現高度依賴訓練樣本品質。若訓練樣本不足、類別定義不清或不同地物光譜相似，模型仍會產生混淆。本次結果顯示，Water 與 Forest 較容易分類，但 Bare、Built-up 與 Cropland 在山區環境中仍容易混淆。這說明從 threshold-based analysis 進階到 classifier-based analysis 後，模型更有彈性，但也更需要良好的樣本設計、外部驗證與不確定性說明。