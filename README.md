# Week 12 Homework: ARIA v8.0

This repository contains the notebook, input data, processed arrays, output figures, and final report for the Week 12 ARIA v8.0 post-earthquake land cover classification homework.

## Notices
- 本次實作有一些不同的操作程序需要注意。首先pystac_client穩定性極差。為避免多次讀取逾時，在讀取後便先將後續會用到的圖片和波段資料存自建的data資料夾。
- 在製作.kmz檔時點選取的polygon必須在研究範圍內，因此首先建立boundary.kml檔並匯入google earth。
- 繳交資料夾為Deliverable，而其中最重要的資料位於TargetFiles資料夾。並且分析內容統一放在Report.md中，以方便閱讀。
## Folder Structure

```text
homework11/
├── ARIA_8.0.ipynb
├── Homework-Week12.md
├── Report.md
├── README.md
├── .gitignore
│
├── data/
│   ├── 20240802新生崩塌地.kml
│   ├── best_item.json
│   ├── boundary.kml
│   ├── trainning_data.kmz
│   ├── img_sr.npy
│   ├── scl.npy
│   ├── X.npy
│   ├── X_valid.npy
│   ├── valid_mask.npy
│   └── preprocess_meta.txt
│
├── outputs/
│   ├── Task1/
│   │   ├── kmeans_classification.png
│   │   ├── kmeans_labeled_map.png
│   │   ├── kmeans_cluster_mean_spectrum.png
│   │   ├── kmeans_cluster_mean_spectrum.csv
│   │   ├── kmeans_cluster_to_class.csv
│   │   ├── kmeans_labels.npy
│   │   ├── kmeans_label_map.npy
│   │   ├── kmeans_class_labels.npy
│   │   └── kmeans_class_map.npy
│   │
│   ├── Task2/
│   │   ├── rf_classification.png
│   │   ├── rf_feature_importance.png
│   │   ├── rf_feature_importance.csv
│   │   ├── kmeans_vs_rf_comparison.png
│   │   ├── rf_class_map.npy
│   │   ├── rf_y_pred_all.npy
│   │   ├── training_data.npy
│   │   └── training_sample_summary.csv
│   │
│   └── Task3/
│       ├── confusion_matrix.png
│       ├── classification_report.txt
│       ├── classification_report.csv
│       ├── partA_accuracy_summary.csv
│       ├── rf_swcb_overlay.png
│       └── swcb_overlap_metrics.csv
│
└── Delieverable/
    ├── kmeans_classification.png
    ├── kmeans_cluster_mean_spectrum.png
    ├── kmeans_cluster_to_class.csv
    ├── kmeans_vs_rf_comparison.png
    ├── partA_accuracy_summary.csv
    ├── rf_classification.png
    ├── rf_feature_importance.png
    └── Target file/
        ├── confusion_matrix.png
        ├── rf_swcb_overlay.png
        └── swcb_overlap_metrics.csv
```

## Main Files

- `ARIA_8.0.ipynb`: Main Jupyter notebook containing all workflow steps.
- `Report.md`: Final markdown report with task-by-task discussion.
- `Homework-Week12.md`: Original homework instructions.
- `data/`: Input data and preprocessed arrays.
- `outputs/`: Full task outputs grouped by task.
- `Delieverable/`: Selected final files for submission.

## Data Description

- `best_item.json`: Selected Sentinel-2 post-earthquake STAC item.
- `img_sr.npy`: Surface reflectance image array with 6 bands.
- `scl.npy`: Sentinel-2 Scene Classification Layer.
- `X.npy`: Full pixel feature matrix.
- `X_valid.npy`: Valid pixel feature matrix after cloud/invalid masking.
- `valid_mask.npy`: Boolean mask for valid pixels.
- `trainning_data.kmz`: Google Earth training polygons for five land cover classes.
- `20240802新生崩塌地.kml`: SWCB official post-earthquake landslide inventory.

## Output Summary

- Task 1 outputs include K-means classification maps and cluster spectral analysis.
- Task 2 outputs include Random Forest classification, feature importance, and K-means vs RF comparison.
- Task 3 outputs include confusion matrix, classification report, SWCB overlay, and spatial overlap metrics.