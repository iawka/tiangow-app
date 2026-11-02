# 天九牌教室：iPhone 及 Android App

這個資料夾是一個完整的 App 專案。遊戲本身（規則、示範、對局）全部放在 `www/index.html`，不用任何網絡資源，裝好後**完全離線**都可以玩。分數會自動儲存在手機內。

專案用 [Capacitor](https://capacitorjs.com) 7 把網頁包裝成真正的 iOS 和 Android App。

```
tiangow-app/
├─ www/                  遊戲本體（index.html）、網頁版離線設定、圖示
├─ android/              Android Studio 專案
├─ ios/                  Xcode 專案（用 Swift Package Manager，不需要 CocoaPods）
├─ assets/               App 圖示和啟動畫面的原圖（1024×1024、2732×2732）
├─ .github/workflows/    兩個自動化流程：建立 APK、發佈網頁版
├─ capacitor.config.json App 名稱、ID、啟動畫面設定
└─ package.json
```

App 名稱：天九牌教室 · App ID：`com.tiangow.classroom`（上架前請改成你自己的，見第 5 節）

---

## 1. Android：最簡單的方法（不用安裝任何軟件）

用 GitHub 的免費雲端電腦幫你建立 APK。

1. 在 GitHub 開一個新的 repository（可以設為 Private）。
2. 把整個資料夾 push 上去（`node_modules` 已在 `.gitignore` 排除）：
   ```bash
   git init
   git add .
   git commit -m "天九牌教室"
   git branch -M main
   git remote add origin https://github.com/<你的帳戶>/tiangow-app.git
   git push -u origin main
   ```
3. Push 之後，「Build Android APK」流程會自動開始。到 repo 的 **Actions** 分頁，等它變成綠色剔號（約 5 分鐘）。
4. 打開那次執行，在最底的 **Artifacts** 下載 `tiangow-android-apk`，解壓得到 `app-debug.apk`。
5. 把 APK 傳到 Android 手機（電郵、Google Drive、USB 都可以），在手機打開它。第一次會問是否允許「安裝不明應用程式」，允許就可以安裝。

以後改了遊戲再 push，就會自動建立新的 APK。

## 2. Android：在你的 Windows 電腦上建立

需要：

- [Node.js](https://nodejs.org) 20 或以上
- [Android Studio](https://developer.android.com/studio)（2024.2.1 或較新版本，已內置 JDK 21 和 Android SDK）

步驟：

1. 解壓這個資料夾，在資料夾內開 PowerShell 或命令提示字元。
2. 安裝套件，然後把遊戲複製到 Android 專案：
   ```bash
   npm install
   npx cap sync android
   ```
3. 用 Android Studio 打開專案：
   ```bash
   npx cap open android
   ```
   或者在 Android Studio 選 **File › Open**，選 `android` 資料夾。第一次打開會下載 Gradle 和 SDK 元件，需時數分鐘。
4. 測試：
   - **模擬器**：**Tools › Device Manager** 建立一部虛擬手機，然後按綠色 ▶ **Run**。
   - **真手機**：在手機開啟「開發人員選項 › USB 偵錯」，用 USB 連接電腦，在上方選你的手機，按 ▶ **Run**。
5. 建立可分享的 APK：**Build › Build App Bundle(s) / APK(s) › Build APK(s)**。完成後按通知中的 **locate**，APK 在 `android/app/build/outputs/apk/debug/`。

## 3. iPhone

Apple 只容許在 **Mac** 上用 Xcode 建立 iPhone App。你用的是 Windows 電腦，所以有兩個選擇：

### 方法 A：網頁版加到主畫面（免費，不需要 Mac）

效果跟 App 差不多：有自己的圖示、全螢幕、第一次打開後可以完全離線使用。

1. 在 GitHub repo 的 **Settings › Pages**，把 **Source** 設為 **GitHub Actions**。
2. 到 **Actions** 分頁，選 **Publish web app › Run workflow**。完成後會得到一個網址，例如 `https://<你的帳戶>.github.io/tiangow-app/`。
   （GitHub 免費帳戶需要 repo 設為 Public 才可以用 Pages。）
3. 在 iPhone 用 **Safari** 打開這個網址，按 **分享 › 加入主畫面**。
4. 在主畫面打開一次，等它完整載入。之後即使開飛航模式也可以玩。

Android 手機也可以用同樣方法：用 Chrome 打開，選 **安裝應用程式** 或 **加到主畫面**。

### 方法 B：用 Mac 建立真正的 iPhone App

需要：一部 Mac、[Xcode](https://apps.apple.com/app/xcode/id497799835) 16 或以上、Node.js 20 或以上。

1. 在 Mac 上解壓資料夾，在終端機執行：
   ```bash
   npm install
   npx cap sync ios
   npx cap open ios
   ```
2. Xcode 打開後，左邊選 **App** 專案 › **Signing & Capabilities**：
   - **Team**：選你的 Apple ID（沒有的話按 **Add Account** 登入）。
   - **Bundle Identifier**：改成獨一無二的，例如 `com.<你的名字>.tiangow`。
3. 用線連接 iPhone，在上方選你的 iPhone，按 ▶ **Run**。
4. 第一次安裝後，在 iPhone 的 **設定 › 一般 › VPN 與裝置管理** 信任你的開發者帳戶。iOS 16 或以上還需要開啟 **設定 › 私隱與保安 › 開發者模式**。

Apple ID 的分別：

| | 免費 Apple ID | Apple Developer Program（每年 US$99） |
|---|---|---|
| 裝在自己的 iPhone | 可以，但 7 天後要用 Xcode 重新安裝 | 可以，一年有效 |
| 用 TestFlight 給家人朋友 | 不可以 | 可以 |
| 上架 App Store | 不可以 | 可以 |

## 4. 修改遊戲

所有遊戲內容都在 `www/index.html`。改完後執行：

```bash
npx cap sync
```

然後在 Android Studio 或 Xcode 重新按 ▶ Run。若用網頁版（方法 A），請同時把 `www/sw.js` 第一行的 `tiangow-v1` 改成 `tiangow-v2`（之後 v3、v4……），手機才會下載新版本。

要換圖示或啟動畫面：替換 `assets/` 內的圖片（尺寸保持不變），然後執行：

```bash
npx capacitor-assets generate --ios --android --iconBackgroundColor "#38241e" --splashBackgroundColor "#38241e"
```

## 5. 上架 Google Play 或 App Store（可選）

只是自己和家人玩的話，不用上架。如果想上架：

- **改 App ID**：把 `capacitor.config.json` 的 `appId` 改成你自己的反向網域，例如 `uk.co.<你的名字>.tiangow`。Android 部分還要在 `android/app/build.gradle` 修改 `applicationId` 和 `namespace`，在 `android/app/src/main/res/values/strings.xml` 修改 `package_name`，並在 `android/app/src/main/java/` 下把 `MainActivity.java` 搬到相應的資料夾。改 App ID 最容易的做法是刪除 `android` 和 `ios` 資料夾，然後重新執行 `npx cap add android` 和 `npx cap add ios --packagemanager SPM`，再執行上面第 4 節的 `capacitor-assets` 指令。
- **Google Play**：一次性付 US$25 開發者帳戶費用。在 Android Studio 用 **Build › Generate Signed Bundle / APK** 建立已簽署的 `.aab`。請妥善保存 keystore 檔案和密碼，遺失了就不能更新 App。新的個人開發者帳戶要先完成封閉測試（多名測試者測試一段時間），才可以正式公開。
- **App Store**：需要 Apple Developer Program（每年 US$99）。在 Xcode 選 **Product › Archive**，然後 **Distribute App** 上傳到 App Store Connect。
- **私隱**：這個 App 不收集任何資料、不連接網絡、沒有廣告。在兩個商店填寫私隱表格時都可以選「不收集資料」。
- **注意**：天九牌在不少地方與賭博有關。App 內沒有真錢或代幣，但商店審核或年齡分級問卷可能會問到「模擬賭博」，請如實填寫。
