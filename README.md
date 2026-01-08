
# FIT-TRACKER 健身訓練日誌 - 部署指南

本專案支援使用 **GitHub Actions** 實現自動化部署，並將資料備份至 Google Sheets。

## 1. 準備 Google Sheets 後端 (GAS)

1. 建立一個新的 Google 試算表。
2. 點擊選單中的 **「延伸功能」 > 「Apps Script」**。
3. 貼入以下內容：

```javascript
function doPost(e) {
  var data = JSON.parse(e.postData.contents);
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  // 覆寫 A1 儲存格作為資料庫
  sheet.getRange("A1").setValue(JSON.stringify(data));
  return ContentService.createTextOutput(JSON.stringify({"result": "success"}))
    .setMimeType(ContentService.MimeType.JSON);
}

function doGet() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  var data = sheet.getRange("A1").getValue();
  return ContentService.createTextOutput(data || "{}")
    .setMimeType(ContentService.MimeType.JSON);
}
```

4. 點擊 **「部署」 > 「新增部署」**。
5. 類型選擇 **「網頁應用程式」**。
6. 設定：執行身份為「我」，誰有權限存取為 **「任何人」**。
7. 部署後複製 **「網頁應用程式 URL」**。

## 2. GitHub Actions 自動部署步驟

1. **上傳程式碼**：將專案所有檔案（包含 `.github` 資料夾）推送到您的 GitHub Repository。
2. **自動建置**：前往 GitHub 上的 **"Actions"** 分頁，您會看到 "Build and Deploy" 流程正在運行。
3. **設定 Pages 來源**：
   *   當 Actions 運行完畢後，GitHub 會自動建立一個 `gh-pages` 分支。
   *   前往 **Settings > Pages**。
   *   在 **Build and deployment > Branch** 選擇 `gh-pages` 並儲存。
4. **完成**：幾分鐘後，您的網站就會在 `https://<您的帳號>.github.io/<專案名>/` 上線。

## 3. 同步資料
1. 開啟您的網站。
2. 點擊左上角 **⚙️ 設定**。
3. 貼入剛才複製的 GAS 網址。
4. 點擊 **📤 上傳備份** 即可開始。

---
*祝您健身順利！*
