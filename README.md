# GoogleMapCrawler

## PART 1 摘要
- `GoogleMapCrawler` 是一個基於 Python 的爬蟲程式，用於從 Google 地圖抓取資料。
- 使用googlemaps等多種函式庫來取得、處理並儲存地理位置的相關資訊，
- 例如該地點的地址、評論、評分等，以滿足從大量資料中找出潛在並且有價值的資訊需求。

## PART 2 功能
- **位置搜尋**：根據關鍵字及座標，並設定範圍進行位置搜尋。
- **資料擷取**：從 Google 地圖擷取資訊，例如名稱、地址、聯絡資訊和評分。
- **評論收集**：收集五個熱門的用戶評論與評分。
- **資料儲存**：以結構化格式（JSON）儲存擷取的資訊。

## PART 3 環境需求
- Python 3.7 以上
- 所需函式庫（透過 `pip` 安裝）：
  ```bash
  !pip install -U googlemaps
  ```
> ### 安裝和使用 `googlemaps` 套件說明

> #### 指令簡介
> `!pip install -U googlemaps` 是在 Jupyter Notebook 中安裝或更新 `googlemaps` 套件的指令。此套件用於使用 Google Maps API 來執行地圖操作，例如地理編碼、路線規劃和距離計算等功能。
> 
> - **`!`**：在 Jupyter Notebook 中表示執行系統指令。
> - **`pip install`**：使用 `pip` 安裝 Python 套件。
> - **`-U`**：更新至最新版本。
> - **`googlemaps`**：套件名稱。
> 
> 1. 安裝完成後，在 Python 程式中導入 `googlemaps` 套件：
> ```python
> import googlemaps
> ```
> 2. 使用您的 **Google Maps API 金鑰** 來初始化 `googlemaps.Client` 以與 Google Maps API 互動：
> ```python
> gmaps = googlemaps.Client(key='YOUR_API_KEY')
> ```
> 
> #### 注意事項
> 1. 您需要擁有 **Google Maps API 金鑰** 並啟用相關服務（例如地理編碼、路線規劃等）才能使用完整功能。
> 2. Google Maps API 可能會產生費用，建議參考 [Google Maps Platform 定價](https://cloud.google.com/maps-platform/pricing) 來確保符合您的需求。


## PART 4 設置步驟

1. 參考網站：[Google Colab 教學 (3)｜儲存、協作及共享 Colab 筆記本](https://medium.com/python4u/google-colab-%E6%95%99%E5%AD%B8-3-%E5%84%B2%E5%AD%98-%E5%8D%94%E4%BD%9C%E5%8F%8A%E5%85%B1%E4%BA%AB-colab-%E7%AD%86%E8%A8%98%E6%9C%AC-aec9a73addd2)
- 點擊儲存的 GoogleMapCrawler.ipynb 後，你可以看到我的 Python 檔案內容。
- 同時因為儲存時有勾選包含 Colaboratory 連結，所以你點擊「Open in Colab」後就可以直接連到 Google Colab 編輯環境。
2. 參考網站 [完整程式參考](https://couplehonest.com/api-python/#jie_lun_pa_chong_ji_API_zhi_shi_qu_de_shu_ju_de_kai_shi_hou_xu_de_zi_liao_shi_yong_ji_fen_xi_cai_shi_guan_jian)
3. 參考網站 [API取得與設置](https://www.weya.com.tw/design/google-map-api-key)

## PART 5 使用方法
1. 在google colab或jupter notebook打開此程式
2. 執行第一個儲存格(ctrl+enter)，安裝所需套件googlemaps
3. 輸入您的 Google Maps API 金鑰
4. 設定max_results 參數，代表最多出現的結果數(可增加)
5. 設定位置座標、範圍和關鍵字
> ```python
> location = "25.03640955354205, 121.52005195201416" # 座標位於：國立中正紀念堂
> radius = 50000  # 擴大範圍可獲取更多景點 50000 = 50KM
> keyword = "spot"  # 可以根據需求更改關鍵字
> ```
6. 設定json檔的檔案名稱 
7. 執行第二個儲存格，執行爬蟲程式
> 擷取的資料將儲存在json格式的檔案中。

## PART 6 程式內容詳細說明

### 1. 初始化 Google Maps API 客戶端

在程式開頭部分，初始化 `googlemaps.Client` 以便使用 Google Maps API。將您的 Google Maps API 金鑰填入 `api_key` 變數中：

```python
api_key = "YOUR_API_KEY"  # 您的 Google Maps API 金鑰
gmaps = googlemaps.Client(key=api_key)
```

### 2. 定義 `get_nearby_spots` 函數

此函數用於從 Google Maps 獲取附近的景點資訊，並支援多頁查詢，以突破 Google Maps API 單頁返回 20 個結果的限制。透過 `max_results` 參數可以設定最多獲取的景點數量。

```python
def get_nearby_spots(api_key, location, radius, keyword, max_results=100):
```

- **參數說明**：
  - `api_key`：Google Maps API 金鑰。
  - `location`：查詢的地理座標（格式為 `"latitude,longitude"`）。
  - `radius`：查詢範圍（以公尺為單位）。
  - `keyword`：搜尋關鍵字。
  - `max_results`：返回結果的最大數量（預設為 100）。

### 3. 景點查詢邏輯

函數內使用 `while` 迴圈來持續請求 Google Maps API，直到獲得所需的結果數量。針對多頁查詢，如果返回了 `next_page_token`，會等待 2 秒後再進行下一頁的請求，以避免 `INVALID_REQUEST` 錯誤。

```python
while len(spots) < max_results:
    if next_page_token:
        time.sleep(2)
        spots_result = gmaps.places_nearby(
            location=location,
            radius=radius,
            keyword=keyword,
            page_token=next_page_token
        )
    else:
        spots_result = gmaps.places_nearby(
            location=location,
            radius=radius,
            keyword=keyword
        )
```

### 4. 提取景點資訊

對於每個獲得的景點，提取以下基本資訊：
- 名稱 (`name`)
- 地址 (`address`)
- 評分 (`rating`)
- 緯度與經度 (`lat`, `lng`)
- 距離（使用 `geopy.distance.geodesic` 計算與查詢座標的距離）

並使用 `gmaps.place` API 查詢景點的詳細資訊，包括熱門評論、電話號碼及網址。

```python
place_id = spot['place_id']
details = gmaps.place(place_id=place_id, fields=["review", "formatted_phone_number", "website"])
```

### 5. 熱門評論擷取

如果景點包含評論，提取每則評論的作者、評分、評論內容和時間，並將其加入 `reviews` 列表中。

```python
if "reviews" in details["result"]:
    for review in details["result"]["reviews"]:
        reviews.append({
            "author_name": review["author_name"],
            "rating": review["rating"],
            "text": review["text"],
            "time": review["relative_time_description"]
        })
```

### 6. 儲存景點資訊

將每個景點的所有資訊，包括名稱、地址、評分、電話、網址、距離及熱門評論，加入 `spots` 列表中。

```python
spots.append({
    "名稱": name,
    "地址": address,
    "評分": rating,
    "電話": phone_number,
    "網址": website,
    "距離": distance,
    "熱門評論": reviews
})
```

### 7. 迴圈結束條件

如果無更多的 `next_page_token` 或者 `spots` 列表中的結果已達 `max_results`，則退出迴圈。

```python
next_page_token = spots_result.get("next_page_token", None)
if not next_page_token:
    break
```

### 8. 結果排序與編號

將 `spots` 按照距離排序，並為每個景點編號。

```python
spots.sort(key=lambda x: x["距離"])
for idx, spot in enumerate(spots, start=1):
    spot["編號"] = idx
```

### 9. 儲存至 JSON 檔案

最終結果會轉為 JSON 格式並儲存為 `sorted_spots_data_with_reviews_contact.json` 檔案：

```python
with open("sorted_spots_data_with_reviews_contact.json", "w", encoding="utf-8") as f:
    json.dump(spots_data, f, ensure_ascii=False, indent=4)
print("已輸出 sorted_spots_data_with_reviews_contact.json 檔案")
```

---

透過以上步驟，程式能有效地從 Google 地圖擷取指定位置的附近景點資訊並儲存為 JSON 格式。
