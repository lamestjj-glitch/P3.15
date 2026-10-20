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
