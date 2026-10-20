# 28Hse 業主篩選陣列格式修復補丁

[下載 hse_filter_array_fix_patch.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/hse_filter_array_fix_patch.zip)

此單檔補丁套用於已安裝首次發現監控補丁的程式資料夾，包含前次 UTF-8 擷取順序及診斷修正。先停止程式，解壓覆蓋原有 `capture_four_lanes.py`，再執行：

```cmd
py -3.15 start_capture.py
```

實際網站以表單送出 `landlordAgency=["LANDLORD"]`，舊程式將整段 JSON 陣列字串與純文字 `LANDLORD` 比較，因而把業主列表判為篩選未匹配。本次只在核對時解析欄位中的 JSON 陣列，原始請求本文不變。重複的 `sortBy=latest` 可接受；不同值、混入仲介值、空陣列、非字串、重複 JSON 鍵衝突仍會拒絕。未通過時分別顯示 landlordAgency 與 sortBy 的固定狀態，不輸出任意原始欄位值。

補丁只有 `capture_four_lanes.py`；429、排程及監控儲存程式未改。四頁依原方式各搜尋一次即可。已從 ZIP 解壓副本執行 114 項本機測試，全過、零跳過，包括實際表單格式、巢狀 form_data、原始 UTF-8 位元組保真及衝突拒絕。另已驗證 Python 3.12 語法與無第三方依賴的 --help；尚未在使用者 Windows／真實網站會話執行新補丁。

ZIP：9,696 bytes，1 個來源檔。SHA-256：`67d07f1d89752b189d075b5a57d38e89816df8aee075adb1bb601df579e79d32`。

---

# 擷取階段 UTF-8 與等待診斷單檔補丁

[下載 capture_decode_fix_patch.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/capture_decode_fix_patch.zip)

此補丁僅含 `capture_four_lanes.py`，套用在已安裝首次發現監控補丁的原程式資料夾。先停止目前程式，解壓覆蓋原檔，再執行：

```cmd
py -3.15 start_capture.py
```

四頁各搜尋一次後等候；無需連續點擊。原本先解碼所有網站背景 POST 的順序已修正：先解析回應並辨識列表，才讀取並嚴格解碼列表請求本文；無關的 binary 背景請求會直接略過。真正列表本文若非 UTF-8 會顯示固定提示並拒絕該模板，沒有 ignore/replace 或猜編碼，不改動重播位元組。

尚未齊全的路線每 15 秒顯示等待診斷：搜尋回應數、HTTP 異常狀態計數、非 JSON、非列表、業主／最新篩選未匹配、非 UTF-8 本文、讀取錯誤、頁面路徑改變、超過大小限制或缺少 UA。只列固定原因及數字，不輸出原始請求、完整 URL、Cookie 或回應內容。

使用者最新畫面顯示 House730 租售已擷取，但未顯示 28Hse 成功；因此仍在四路擷取階段，尚未進入 ID 基線監控。此補丁修正背景 POST 解碼誤報並提供診斷；28Hse 未成功的實際原因仍須依新診斷確認。429、排程與已保存監控狀態不變。

已由補丁解壓副本執行 105 項本機測試，全過且零跳過，含新4項背景二進位忽略、真實非 UTF-8 模板拒絕、原篩選保留及中文本文保真。尚未在使用者的 Windows／網站會話驗證新修正。

ZIP：8,946 bytes，1 個來源檔。SHA-256：`1b029e3f54c9f71ce079c2be1b107b860bf4b49c849def4386842bfa67b5924f`。

---

# 第一頁首次發現與持久去重補丁

[下載 first_seen_monitor_patch.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/first_seen_monitor_patch.zip)

將補丁的五個 Python 檔案全部放入原程式資料夾（覆蓋／新增）。停止舊程式，再執行：

```cmd
py -3.15 start_capture.py
```

四個列表分頁都設定業主、最新排序並停在第 1 頁，依提示輸入實際 CDP 埠與間隔，各手動搜尋一次。一般啟動模式已接上新監控，不用加額外旗標；`--export-samples` 仍只匯出樣本。

- 第一個有效回應只顯示基線筆數，不逐個輸出已有盤。
- 後續只列「首次發現」的公開 ID 與可驗證連結；重複 ID、消失後再出現、較舊請求編號的延遲回應不重複報。
- 每 60 秒印統計；結果在 `monitor_data/new_listings.jsonl`，狀態在 `monitor_data/seen.sqlite3`。保留整個資料夾，重啟後繼續去重；更改搜尋條件另建基線。
- 28Hse 僅讀主列表 HTML；House730 僅讀 `result.data`，不將貸款、推薦廣告旁支視為房源。失敗或未知格式不會清掉基線。

首次發現只表示本程式第一次看到該 ID，不能證明剛發布。只監控第一頁可辨識 ID，不自動翻頁，離線或限流期間可能漏過滑出第一頁的盤。尚未加入電話查詢、價格變動或外部通知。

429、頻率預設及原有 HTTP 排程程式碼逐位元組保持不變。Cookie、UA、原始請求與完整回應不寫入監控結果；上傳的使用者樣本僅在本機驗證，未包含在公開補丁。詳見壓縮檔內 README_FIRST_SEEN.txt。

從補丁解壓副本套用到測試目錄後，101 項本機測試全過、零跳過，包括真實本機 HTTP 輪詢至新 ID 存檔、CDP、亂序、重啟去重與輸出失敗恢復；尚未在原生 Windows 或實際兩站執行新監控模式。

ZIP：18,756 bytes，5 個來源檔及1份說明。SHA-256：`95865716a1961a6e8b07eeeb2680c96b7b0f5fb905ddc0227e54ae981b0a42e9`。

---

# 一次性匿名回應樣本匯出補丁

[下載 capture_samples_patch.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/capture_samples_patch.zip)

1. 停止原本的持續輪詢。把補丁的 `capture_four_lanes.py` 及 `sample_export.py` 放回原程式資料夾（覆蓋／新增）。
2. 在原資料夾 CMD 執行：

```cmd
py -3.15 start_capture.py --export-samples
```

3. 輸入實際 CDP 埠並確認四頁篩選；在兩站租售的四個分頁各手動搜尋一次。列表需能正常回傳。
4. 成功後會在原程式資料夾產生 `samples_for_review.zip`。先解壓檢查內容，再將這個新產生的 ZIP 拖入聊天上傳。

此模式只監聽既有瀏覽器的回應、匯出後退出；不啟動 curl_cffi 輪詢，也不變更 429 處理。原先只印 dict 的日誌沒有保存歷史回應，無法由舊日誌還原樣本。

樣本 ZIP 包含四路 JSON 與說明，每個陣列最多三筆並記錄原長度。未知葉值匿名，保留可讀欄位名、部分公開房源 ID 及分頁／狀態值；HTML 提供受限骨架。Cookie、headers、UA 與完整請求網址不匯出。自動處理無法辨識所有可能的動態欄位名，分享前仍需檢查；這是結構素材，不能作為可執行配置。若 ZIP 已存在，程式停止而不覆寫，可用 `--export-samples another-samples.zip` 換名。

已從補丁解壓副本套用到測試目錄執行 64 項本機測試，全數通過且零跳過，包括真實本機 Chromium/CDP、單次匯出、匿名化及既有檔案保護；未在原生 Windows 或實際站台執行新匯出模式。

補丁 ZIP：12,907 bytes，兩個 Python 來源檔。SHA-256：`e2268ee00535ad2fd7c0c2ba922af90f5a32a08e2f488c919945300e47e1e28a`。

---

# Chrome profile 配對單檔補丁

[下載 capture_profile_patch.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/capture_profile_patch.zip)

適用情況：Python 已啟動、四路列表已擷取，但出現「原瀏覽器版本沒有可自動選定的 profile」。

此 ZIP 只包含 `capture_four_lanes.py`，備份原檔後解壓覆蓋到既有程式資料夾，再執行：

```cmd
py -3.15 start_capture.py
```

自動辨識桌面 Chrome 後，優先選同主版本 profile；curl_cffi 沒有該版本時，以數字版本順序選最近較舊的桌面 Chrome profile 並顯示近似配對警告。主版本與選定 profile 會按站顯示；原始 UA、Cookie、標頭和 Body 不進入診斷日誌。保留各站顯式 override；無法識別桌面 Chrome 或低於最舊支援版本時，會提供具體的每站 override 參數。Edge、Android、iOS 等不會自動誤配桌面 Chrome。

8 項 profile 回歸測試及 6 項擷取／本機 CDP 測試全部通過。近似 profile 並不保證網站接受；403 或已辨識的驗證頁仍按既有機制停止同站請求。此補丁尚未在使用者的實際兩站會話驗證。

ZIP：7,564 bytes，1 個檔案。SHA-256：`a97c224acbf7fb5d49e86cb8972401ce19eeb8604a7b61a6768552a9e87dbda2`。

---

# Python 3.15 啟動路徑修復補丁

[下載 python315_startup_patch.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/python315_startup_patch.zip)

適用情況：Windows 顯示 `Failed to launch ... Python313\python.exe (0x80070003)`，但已有可用的 Python 3.15。此錯誤發生在 Python 程式執行前。

將補丁全部解壓到原程式資料夾，覆蓋同名檔案，接著在該資料夾的 CMD 執行：

```cmd
py -3.15 start_capture.py
```

補丁共三個來源檔：`START_CAPTURE.cmd`、`start_capture.py`、`capture_four_lanes.py`，依賴現有的 `async_crawler.py`、`four_lane_crawler.py` 及兩份 requirements 檔案。

啟動器會先確認直譯器能實際執行；既有 `.crawler-venv` 無法執行、缺少 Python 或版本不同時，會保留為 `.crawler-venv.backup-*` 並以指定的 Python 重建。依賴會按原本 requirements 安裝。CMD 也支援 `CRAWLER_PYTHON` 指定完整 exe 路徑，並逐一測試 PATH 候選。

18 項啟動回歸測試通過；已在 Linux Python 3.12 重現失效直譯器、驗證備份保留、真實依賴重裝及暖啟動重用。已確認全部依賴存在 Windows x64／CPython 3.15 相容 wheel；尚未原生執行 Windows 或 Python 3.15，也尚未驗證兩站線上 API。

ZIP：10,298 bytes。SHA-256：`80b499be219db15a54ba782f0a10d09c6216013d84d7191ab3e61e70ff7467e5`。

---

# Python 單檔啟動補丁

[下載 capture_four_lanes_python_patch.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/capture_four_lanes_python_patch.zip)

此補丁只包含 `capture_four_lanes.py`。先備份原檔，解壓後覆蓋到原程式目錄，再使用目前的 Python 執行：

```text
python capture_four_lanes.py
```

保留既有核心、依賴與設定；不附啟動器。無參數時互動輸入 CDP 與間隔，Windows 直接開啟無參數的 `.py` 後會保留視窗；缺依賴時顯示 Python 安裝指令。保留舊版可替換的函式介面。

原 ZIP 單檔覆蓋後 22 項測試通過，另有 11 項啟動回歸測試通過。測試環境為 Linux Python 3.13；尚未實機測試 Python 3.15 或原生 Windows，未變更套件版本需求。

ZIP 7,045 bytes，1 個檔案。SHA-256：`cca4c8cd2fe7be8fb4eee5816249505b963e9a52a1ecf8e68385ba0b94633d1d`

---

# 四路爬蟲下載：Windows 啟動修正版

下載 [curl_cffi_four_lanes_verified.zip](https://github.com/lamestjj-glitch/P3.15/raw/refs/heads/crawler-package-download/curl_cffi_four_lanes_verified.zip)。

1. 安裝 Python 3.12 或更新版本（建議 Python 3.13）。
2. 將整個 ZIP 解壓到可寫入的資料夾，再雙擊 `START_CAPTURE.cmd`。
3. 首次會建立獨立的 `.crawler-venv` 並安裝依賴，需要網路。
4. 依提示輸入 BitBrowser 實際的 CDP 調試埠；它不是 Local API 的 `54345`。預先開啟四個租售列表分頁並確認篩選，按程式提示逐頁搜尋。

啟動或執行失敗後，視窗會停留顯示訊息。`logs/startup.log` 只記錄啟動階段與退出碼。從終端也可執行 `python start_capture.py --check-setup` 單獨檢查環境。

內容包含 asyncio/curl_cffi 四路排程（預設每路 500ms，可調）、同站並發管理、403 停止與提示、429 冷卻、既有瀏覽器 CDP 請求擷取，以及缺依賴／啟動失敗診斷。完整設定與限制見壓縮檔內 `README_CRAWLER.md`。

已從候選 ZIP 的解壓副本執行 **33 項測試全部通過，0 跳過**；另以 Linux Python 3.12 實測全新依賴安裝及再次啟動重用環境，含中文與空格路徑。原生 Windows CMD 尚未實機執行，兩站線上 API 尚未驗證；不自動完成 Turnstile。程式尚未串接完整的新房源去重、儲存與電話查詢管線。

ZIP：42,676 bytes，16 個檔案；已驗證 CRC、SHA-256 manifest 與來源內容。

SHA-256：`4a2790b6abd25c733f4e2be35cdc3fc9b1ff98275001fad8c8517f62b0b63a31`

本分支保存下載成品，原始 main 分支保持原狀。
