# GoogleMapCrawler

## 概述
`GoogleMapCrawler` 是一個基於 Python 的程式，用於從 Google 地圖抓取資料。此程式使用多種函式庫來取得、處理並儲存地理位置的相關資訊，例如位置詳細資料、評論、評分等，以滿足特定的應用需求。

## 功能
- **位置搜尋**：根據關鍵字或座標進行位置搜尋。
- **資料擷取**：從 Google 地圖擷取資訊，例如名稱、地址、聯絡資訊和評分。
- **評論收集**：收集五個熱門的用戶評論與評分。
- **資料儲存**：以結構化格式（JSON）儲存擷取的資訊。

## 環境需求
- Python 3.7 以上
- 所需函式庫（透過 `pip` 安裝）：
  ```bash
  pip install requests beautifulsoup4 selenium pandas
  ```
- Google Chrome 及 ChromeDriver（用於 Selenium）

## 設置步驟
1. 複製此專案：
   ```bash
   git clone https://github.com/yourusername/GoogleMapCrawler.git
   ```
2. 安裝所需依賴：
   ```bash
   pip install -r requirements.txt
   ```
3. 確保 `ChromeDriver` 在您的 PATH 中，或在程式碼中指定路徑。

## 使用方法
1. 打開程式碼，設定搜尋參數，例如位置關鍵字、結果數量等。
2. 執行爬蟲程式：
   ```bash
   python GoogleMapCrawler.py
   ```
3. 擷取的資料將儲存在指定格式的輸出資料夾中。
