# ARIA v4.0 — Accessible Rescue Infrastructure Analyzer

**Week 7 Assignment | RSGI | National Taiwan University**
**Event: Typhoon (海葵颱風) | Area: Hualien City, Taiwan**

---

# catalogue
1. project overview
2. data sources
3. .env數值設定
4. workflow
5. key results
6. AI Diagnostic Log
7. reflection

---

## Project Overview

ARIA v4.0 upgrades the disaster accessibility system to assess how road network
accessibility collapses during typhoon events through network analysis.

---

## Data Sources

| Data | Source | File |
|------|--------|------|
| Road Network | OSMnx (OpenStreetMap) | `hualien_drive.graphml` |
| Shelter Locations | Week 3 Output | `shelter_hualien_df.geojson` |
| Terrain Risk | Week 4 Output | `terrain_risk_audit.json` |
| Rainfall Data | CWA Station Data | `rain_20230904.csv` |
| Administrative Boundary | TGOS | `TOWN_MOI_1140318/` |

---

## .env數值設定

* Week 7 additions — 路網分析
```
NETWORK_DIST=5000
ISOCHRONE_MINUTES=5,10
```
* 塞車係數門檻（降雨 mm → 壅塞因子）
```
# <10mm → cf=0, 10-40mm → cf=0.3, 40-80mm → cf=0.6, >80mm → cf=0.9
CONGESTION_METHOD=threshold
CONGESTION_BREAK_1=10
CONGESTION_BREAK_2=40
CONGESTION_BREAK_3=80
```

---

## Workflow

1. **Road Network Extraction** — Fetch Hualien City drive network via OSMnx, project to EPSG:3826, calculate `travel_time` per edge
2. **Bottleneck Analysis** — Compute Betweenness Centrality, identify Top 5 bottleneck nodes, overlay with terrain_risk
3. **Rainfall Integration** — Load station rainfall (HOUR_6), build 5km buffers, sjoin to road nodes, compute congestion factor `cf`
4. **Dynamic Weights** — Apply `travel_time_adj = length / (speed × (1 − cf))` to each edge
5. **Isochrone Analysis** — Compute 5min/10min isochrones before and after disaster for 5 key facilities
6. **Isolation Detection** — Flag shelters where nearby road avg_cf > 0.7
7. **AI Strategy Briefing** — Generate disaster response recommendations via Gemini API

---

## Key Results

### Top 5 Bottleneck Nodes

| Rank | Node ID | Centrality | Terrain Risk |
|------|------------|------------|--------------|
| 1 | 1119847832 | 0.2312 | High |
| 2 | 601584086 | 0.2156 | Medium |
| 3 | 906775386 | 0.2113 | Low |
| 4 | 906775240 | 0.1966 | Medium |
| 5 | 883096197 | 0.1940 | Medium |

### Accessibility Impact Summary

| Facility | Shrinkage 5min | Shrinkage 10min |
|----------|---------------|----------------|
| 鹽寮活動中心 | 88.2% | 66.7% |
| 大興活動中心 | 78.7% | 65.9% |
| 清水社區活動中心 | 78.7% | 65.9% |
| 宜昌活動中心 | 73.0% | 52.6% |
| 中華國小 | 50.2% | 29.7% |

**Isolated Facilities (avg_cf ≥ 0.7):** 16 shelters identified

---

## AI Diagnostic Log

### Problem: Missing Road Speed Attributes — Default Value Strategy

**Issue:**
During road network construction, many edges in the Hualien OSM dataset lacked
`maxspeed` attributes. Using a single default speed for all road types produced
unrealistic travel time estimates.

**Solution:**
Implemented a highway-type-based speed mapping before falling back to the global
default:

```python
DEFAULT_SPEED_KMH = 40

for u, v, data in G.edges(data=True):
    maxspeed = data.get("maxspeed", None)
    speed = DEFAULT_SPEED_KMH
    if maxspeed is not None:
        try:
            if isinstance(maxspeed, list):
                maxspeed = maxspeed[0]
            raw = str(maxspeed).strip()
            is_mph = "mph" in raw.lower()
            speed = float(raw.lower().replace("mph", "").strip())
            if is_mph:
                speed *= 1.60934
        except (ValueError, TypeError):
            speed = DEFAULT_SPEED_KMH
    data["speed_kph"] = speed
    data["travel_time"] = data["length"] / (speed / 3.6)
```
---

## reflection
* 這次的作業非常有趣，結合了交通工程到測量分析中，對我自己的研究有很大的幫助。
* 原先使用week6的kriging_rainfall_20251111.tif作為降雨參考資料，但發現花蓮測站的雨量過低沒有降低車速的必要，因此最後選取了海葵颱風(2024-09-04)的降雨資料。
* 由於免費gemini額度已用完，因此向claude請求製作AI Strategy Briefing並最後順利完成。
* 在節點的分析使用中點，這在短路徑上較為準確，但是在教長度鏡中可能會出現偏差，因此若要提升預測能力可以考慮在更多處觀測雨量，例如額外多取道路25%, 75%位置的雨量資料。
