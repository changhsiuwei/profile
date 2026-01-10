# GitHub Pages 網站部署教學

本文件將引導您如何將製作完成的學術個人網站部署到 GitHub Pages，讓全世界都能透過網址訪問您的網站。

---

## 方法一：快速部署（推薦給非開發者）

如果您不想安裝複雜的開發工具，只想最快速地讓網站上線，請採用此方法。

### 步驟 1：準備檔案
1. 請解壓縮本專案提供的 ZIP 檔案。
2. 找到資料夾中的 **`dist`** 資料夾。
   - `dist` 資料夾內通常包含 `index.html` 以及 `assets` 資料夾。
   - **請注意：** 您只需要上傳 `dist` 資料夾內的「所有內容」，而不是上傳 `dist` 資料夾本身。

### 步驟 2：建立 GitHub Repository
1. 登入您的 [GitHub](https://github.com/) 帳號。
2. 點擊右上角的 **+** 號，選擇 **New repository**。
3. 在 **Repository name** 欄位輸入您想要的專案名稱（例如 `my-academic-profile`）。
4. 勾選 **Public**（公開）。
5. 點擊 **Create repository**。

### 步驟 3：上傳檔案
1. 在建立好的 Repository 頁面中，點擊 **uploading an existing file** 連結。
2. 將 **`dist` 資料夾內的所有檔案**（包含 index.html 與 assets 資料夾）直接拖曳到網頁上傳區。
3. 等待檔案上傳完畢後，在下方的 **Commit changes** 區塊，直接點擊綠色的 **Commit changes** 按鈕。

### 步驟 4：設定 GitHub Pages
1. 點擊 Repository 上方選單的 **Settings**（設定）。
2. 在左側選單中找到 **Pages** 選項並點擊。
3. 在 **Build and deployment** 區塊下的 **Source** 選擇 **Deploy from a branch**。
4. 在 **Branch** 選單中，選擇 **main** (或 master) 分支，右側資料夾選擇 **/(root)**。
5. 點擊 **Save**。

### 步驟 5：完成
約等待 1-2 分鐘後，重新整理 Pages 設定頁面。您會在上方看到類似 `https://your-username.github.io/my-academic-profile/` 的網址，點擊即可瀏覽您的網站！

---

## 方法二：完整原始碼部署（推薦給開發者）

如果您熟悉 Git 操作，或未來希望能持續修改網站程式碼，建議採用此方法。

### 步驟 1：建立 Repository
與方法一相同，先在 GitHub 建立一個空的 Repository。

### 步驟 2：推送原始碼
在您的電腦上，使用終端機（Terminal）或 Git 工具將專案推送到 GitHub：

```bash
# 初始化 Git
git init

# 加入所有檔案
git add .

# 提交變更
git commit -m "Initial commit"

# 連結到遠端 Repository (請將 URL 換成您的)
git remote add origin https://github.com/USERNAME/REPO_NAME.git

# 推送程式碼
git push -u origin main
```

### 步驟 3：設定 GitHub Actions (自動部署)
本專案已設定好適合 GitHub Pages 的配置。您只需在 GitHub Repository 設定自動化流程：

1. 進入 Repository 的 **Settings** > **Pages**。
2. 在 **Source** 選項中，改為選擇 **GitHub Actions**。
3. GitHub 會自動偵測並建議適合的 Workflow (通常是 Static HTML 或 Vite)，或者您可以手動建立 `.github/workflows/deploy.yml`。

若您選擇手動建立，請在專案根目錄建立 `.github/workflows/deploy.yml`，內容如下：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Set up Node
        uses: actions/setup-node@v4
        with:
          node-version: 20
      - name: Install dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

設定完成後，每次您 push 程式碼到 main 分支，GitHub 就會自動重新建置並部署網站。

---

## 常見問題 (FAQ)

**Q: 網站圖片無法顯示？**
A: 請確保您的 `vite.config.ts` 中的 `base` 設定正確。本專案已預設為 `base: "./"`，這通常能相容於大多數 GitHub Pages 的路徑。

**Q: 更新網站後看不到變更？**
A: 瀏覽器可能會快取舊的檔案。請嘗試使用 **Ctrl + F5** (Windows) 或 **Cmd + Shift + R** (Mac) 強制重新整理頁面。
