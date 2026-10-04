# 消防署大量傷病患救護管理與檢傷練習模擬系統
### Mass Casualty Incident (MCI) Simulation Tool

[![GitHub Pages](https://img.shields.io/badge/Deployment-GitHub%20Pages-brightgreen)](https://pages.github.com/)
[![Architecture](https://img.shields.io/badge/Architecture-Single--File%20(Pure%20HTML%2FJS)-blue)](#系統架構與技術堆疊)
[![Protocol](https://img.shields.io/badge/Realtime-MQTT%20over%20WebSocket-orange)](https://www.emqx.com/)
[![Tailwind CSS](https://img.shields.io/badge/CSS-Tailwind%20CDN-38bdf8)](https://tailwindcss.com/)
[![License](https://img.shields.io/badge/License-MIT-gray.svg)](LICENSE)

本專案為針對消防機關、救護技術員（EMT/EMTP）及大型演習單位所設計的**大量傷病患（MCI）檢傷分類與後送管理練習模擬器**。

採用**純前端單一檔案架構（`index.html`）**，完全無需建置後端伺服器與資料庫，具備跨裝置響應式排版（手機、平板、指揮站電腦大螢幕）；透過公共 MQTT 協定實作雲端多機雙向即時同步，能同時支援現場搜救檢傷人員手機填報、檢傷官/前進指揮所即時監控儀表板，以及實體雙面傷票的工業級列印輸出。

---

## 🌟 核心特色

- **免後端、即開即用**：僅需單一 `index.html` 檔案即可運行，可直接以瀏覽器本地開啟或部署至 GitHub Pages。
- **多機雙向即時雲端同步**：運用公共 MQTT WebSocket Broker 與 `retain` 機制，支援演習中多部手機、平板與電腦大螢幕即時連動。
- **高保真復刻**：依據台灣消防署大量傷病患救護資訊系統之實際執勤標準與視覺介面規格進行 1:1 復刻。
- **實體傷票物理鏡像列印**：支援 A3 橫向、A4 直向與 A4 縮印，正反面自動鏡像倒序排列，確保雙面印刷後撕條與裁切線 100% 吻合。
- **多單位分房機制**：透過自訂 MQTT Topic 或切換部署路徑，即可完全隔離不同大隊或分隊的演習資料。

---

## 📱 三大核心功能模組

### 1. 行動端檢傷追蹤系統 (Mobile First)
專為救護人員手機直向操作打造，全域字體與元件間距經人體工學舒適化調整：
- **案件列表 (`#mobile-view-cases`)**：內建「巴陵分隊練習案件」及歷史演習案件切換。
- **檢傷統計看板 (`#mobile-view-list`)**：採用 6 欄等寬網格（`grid-cols-6`），黑、紅、黃、綠人數垂直置中嚴格對齊。
- **QR-Code 虛擬感應浮動鈕**：點擊直接呼叫傷卡序號挑選視窗（支援 `SM263601`～`SM263650`），直覺選取未登記傷票或檢視已登錄傷患。
- **檢傷分類卡 (`#mobile-view-detail`)**[cite: 1]：
  - 檢傷編碼採用灰底襯線斜體字標記[cite: 1]。
  - 2×2 檢傷級別實體按鈕（I 紅、II 黃、III 綠、0 黑）[cite: 1]。
  - 車輛縣市與後送車輛預設「請選擇 ▾」[cite: 1]。
  - 內建桃園與鄰近 17 家責任醫院，系統依據座標直線距離自動由近至遠排序[cite: 1]。
  - **離開現場時間業務邏輯**：僅在「後送車輛」與「後送醫院」兩者皆已明確選擇並儲存時，自動寫入當下時間戳記（`departTime`）[cite: 1]。

### 2. 實體傷卡生成列印系統 (A3 / A4 自由切換)
完全符合物理印刷規範，列印時自動隱藏系統選單，直接輸出乾淨標準頁面[cite: 1]：
- **紙張規格**[cite: 1]：
  - **A3 橫向 (預設)**：每面 5 張，1:1 原寸（單張 80mm × 215mm），短邊翻轉，高度嚴格鎖定 225mm，10 人傷票標準輸出 4 頁[cite: 1]。
  - **A4 直向 (一般印表機專用)**：每面 2 張，1:1 實體原尺寸（80mm × 215mm），長邊翻轉，正反面自動鏡像重疊[cite: 1]。
  - **A4 橫向 (縮印版)**：每面 5 張，等比縮印 70.7%[cite: 1]。
- **印刷鏡像技術**：正面採正序排列 `[卡1, 卡2, ...]`，背面自動採倒序排列 `[... 卡2, 卡1]`，雙面列印對翻後裁切線與撕條精準吻合[cite: 1]。
- **印刷細節復刻**：對稱斜向打孔虛線、Code 128 一維條碼、QR-Code、撕條尺寸白標記、人體胸骨 v 槽與背面脊椎/肩胛骨線條、3 行生命徵象記錄表格[cite: 1]。

### 3. 大傷前進指揮所管理系統 (大傷儀表板)
1:1 復刻消防署官方藍白風格 UI（背景色 `#bfe2f5`），導入跨螢幕自適應階梯字體（電腦螢幕與大電視自動放大至 24px～27px）[cite: 1]：
- **頂部 Header 與數據方塊**：四色統計徽章（總計、黑、紅、黃、綠）及現場比例、送醫比例動態計算[cite: 1]。
- **傷患後送數據側邊欄**：統計後送中人數、到院人數、未送醫人數與車輛數（重置資料時精準歸零為 `0 臺`）[cite: 1]。
- **3-1 救護站列表**：支援項次、站號、站名、起訖時間，以四色微型方塊垂直堆疊（Micro-Stack）呈現各級別分流統計[cite: 1]。
- **3-2 傷病患清單**：支援 5 / 10 / 50 筆動態分頁與級別/狀態過濾；內建 1:1 復刻「傷患評估」彈跳視窗（未填寫項目嚴格保持空白）及「特徵照片」未開放警示提示[cite: 1]。
- **3-3 後送醫院收治情形**：收錄責任醫院醫療能力與容納量（如長庚重度/40）；**智慧過濾機制**自動僅顯示目前有病患「送醫中」或「已到院」的醫院[cite: 1]。

---

## 🛠️ 系統架構與技術堆疊

- **核心技術**：原生 HTML5 + JavaScript (Vanilla ES6+)[cite: 1]
- **樣式引擎**：[Tailwind CSS CDN](https://tailwindcss.com/)[cite: 1]
- **條碼生成**：[JsBarcode CDN](https://github.com/lindell/JsBarcode) (Code 128)[cite: 1]
- **二維碼生成**：[QRCode.js CDN](https://github.com/davidshimjs/qrcodejs)[cite: 1]
- **通訊協定**：[MQTT.js CDN](https://github.com/mqttjs/MQTT.js) (WebSocket WSS)[cite: 1]
- **通訊節點配置**[cite: 1]：
  - **MQTT Broker**：`wss://broker.emqx.io:8084/mqtt`[cite: 1]
  - **預設 Topic**：`tw_mci_baling_exercise_live_2026/data`（QoS: 1, `retain: true`）[cite: 1]
  - **本地多頁廣播**：`BroadcastChannel('mci_sim_channel')` 同步跨分頁[cite: 1]
  - **持久化儲存**：`localStorage` 鍵名 `mci_sim_patients_db`（15 秒心跳安全校驗）[cite: 1]

---

## 📂 資料結構 (Data Schema)

系統傷病患資料儲存於單一物件字典，傷票編號一律強制遵循 **`SM`** 開頭序號規範[cite: 1]：

```javascript
{
  "SM00T814": {
    "id": "SM00T814",              // 傷票序號 (SM 開頭)[cite: 1]
    "level": "red",                // 檢傷級別: 'red' | 'yellow' | 'green' | 'black'[cite: 1]
    "name": "積積",                // 姓名 (未填預設為 '不詳')[cite: 1]
    "age": 9,                      // 年齡 (數字，預設 0)[cite: 1]
    "gender": "男",                // 性別: '男' | '女'[cite: 1]
    "phone": "",                   // 電話 (選填，未填保持空字串)[cite: 1]
    "idcard": "",                  // 身分證字號 / 護照號碼[cite: 1]
    "features": "很壯",            // 患者特徵 (未填保持空字串)[cite: 1]
    "notes": "會一直喊雞雞雞雞...",  // 補充說明 (未填保持空字串)[cite: 1]
    "station": "第一救護站",        // 救護站名[cite: 1]
    "city": "桃園市",              // 車輛縣市 (未選為空字串)[cite: 1]
    "vehicle": "巴陵91",           // 後送車輛 (未選為空字串)[cite: 1]
    "hospital": "國軍桃園總醫院",    // 後送醫院 (未選為空字串)[cite: 1]
    "arrived": false,              // 是否到達醫院 (true | false)[cite: 1]
    "temsis": "",                  // TEMSIS 病歷碼[cite: 1]
    "time": "10:20:11",            // 最後更新時間戳 (HH:mm:ss)[cite: 1]
    "departTime": "10:20:11"       // 離開現場時間 (同時選取車輛與醫院儲存時寫入，否則為空字串)[cite: 1]
  }
}
