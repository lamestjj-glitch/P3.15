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
