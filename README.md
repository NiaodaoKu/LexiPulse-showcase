<div align="center">

<img src="icon.png" width="120" alt="LexiPulse Icon" />

# ⚡ LexiPulse

**科學化間隔重複單字記憶系統**

[![Live Demo](https://img.shields.io/badge/🔗_Live_Demo-lexipulse.pages.dev-6366f1?style=for-the-badge)](https://lexipulse.pages.dev)
[![Cloudflare Pages](https://img.shields.io/badge/Hosted_on-Cloudflare_Pages-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://pages.cloudflare.com)
[![Google Apps Script](https://img.shields.io/badge/Backend-Google_Apps_Script-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://script.google.com)
[![PWA](https://img.shields.io/badge/PWA-Installable-5A0FC8?style=for-the-badge&logo=pwa&logoColor=white)](https://lexipulse.pages.dev)

*打造你的專屬單字庫 — 讓艾賓浩斯遺忘曲線替你決定何時該複習*

</div>

---

## ✨ 功能亮點

| 功能 | 說明 |
|------|------|
| 🧠 **SRS 間隔重複** | 自研 SRS 演算法，依據遺忘曲線動態排程複習時機 |
| 📸 **AI 輔助錄入** | 拍照辨識單字 / AI 生成例句與提示 |
| 📚 **單字 & 句子庫** | 分開管理單字組與句子，支援語境提示（context）欄位 |
| ⭐ **收藏 & 分類** | 星號收藏、自訂卡組標籤，快速篩選 |
| 📊 **月度回顧** | 每月統計新增單字數與複習次數 |
| 📵 **完整離線支援** | Service Worker 快取，離線操作自動排隊，上線後同步 |
| 📱 **可安裝 PWA** | iOS / Android 均可加到主畫面，體驗近似原生 App |

---

## 🏗️ 系統架構

```
┌─────────────────────────────────────────────────┐
│                   使用者瀏覽器                     │
│                                                  │
│  index.html (Vanilla JS + Tailwind CSS)          │
│  ├─ Google Identity Services (GIS 登入)           │
│  ├─ Service Worker (sw.js) → 離線佇列             │
│  └─ PWA Manifest                                 │
└────────────────────┬────────────────────────────┘
                     │ HTTPS POST
                     ▼
┌─────────────────────────────────────────────────┐
│         Cloudflare Pages Functions               │
│              functions/api.js                    │
│                                                  │
│  ⚙️  GAS 302 Redirect Proxy                      │
│  → POST 觸發 GAS 執行                             │
│  → GET redirect URL 取回結果                      │
│  → 繞過瀏覽器 CORS 限制                            │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│          Google Apps Script (後端)               │
│               程式碼.js                           │
│                                                  │
│  doPost() → action router → 30+ API functions   │
│  ├─ 用戶管理 (getUserInfo, markWelcomed...)       │
│  ├─ 單字 CRUD (addWords, editWord, delete...)    │
│  ├─ SRS 引擎 (dailyReset, completeOneRound...)   │
│  ├─ 錯題庫 (saveWrongAnswer, recordCorrect...)   │
│  └─ 分類系統 (getUserCategories, updateSet...)   │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│            Google Sheets (資料庫)                 │
│                                                  │
│  📋 用戶索引  → 所有用戶基本資料 & 星號 & 分類      │
│  ❌ 錯題庫   → 跨用戶統一錯題追蹤                  │
│  👤 {userKey} → 每位用戶一個 Sheet，儲存單字組     │
└─────────────────────────────────────────────────┘
```

---

## 🧬 SRS 演算法

自研間隔重複排程，無需外部套件：

```
輪次 0（當天錄入）  → 複習 3 次
輪次 1（隔 1 天）   → 複習 3 次
輪次 2（隔 1 天）   → 複習 2 次
輪次 3（隔 1 天）   → 複習 2 次
輪次 4（隔 1 天）   → 複習 1 次
輪次 5+            → 複習 1 次，間隔 max(2, round-3) 天
```

錯題庫採**連續答對 3 次**自動畢業機制，強化薄弱點。

---

## 🛠️ 技術棧

| 層級 | 技術 | 說明 |
|------|------|------|
| **前端** | Vanilla JS + Tailwind CSS | 零框架，輕量快速 |
| **PWA** | Service Worker + Web App Manifest | 離線支援 + 可安裝 |
| **託管** | Cloudflare Pages | 全球 CDN，免費方案 |
| **API Proxy** | Cloudflare Pages Functions | 解決 GAS CORS 問題 |
| **後端** | Google Apps Script | Serverless，免費執行 |
| **資料庫** | Google Sheets | 免費、有 UI、易維護 |
| **身份驗證** | Google Identity Services (GIS) | OAuth 2.0，無密碼 |

> **架構亮點**：整套系統運行成本為 **$0**，完全基於免費服務，卻擁有完整的多用戶系統、REST-like API 設計、PWA 離線能力與自研 SRS 演算法。

---

## 📁 專案結構

```
LexiPulse/
├── 程式碼.js          # GAS 後端（所有 API，30+ functions）
├── index.html         # 前端 SPA（UI + 前端邏輯）
├── sw.js              # Service Worker（離線 + 背景同步）
├── manifest.json      # PWA manifest
├── appsscript.json    # GAS 設定（權限、時區）
├── .clasp.json        # clasp CLI 設定
├── functions/
│   └── api.js         # Cloudflare Pages Function（GAS Proxy）
└── *.ps1              # PowerShell patch 腳本（功能熱修復）
```

---

## 🚀 部署流程

### 後端（Google Apps Script）

```bash
npm install -g @google/clasp
clasp login
clasp push
# → GAS 編輯器：部署 → 管理版本 → 選「新版本」→ 部署
```

### 前端（Cloudflare Pages）

Push 到 GitHub 後，Cloudflare Pages 自動觸發部署。

```bash
git add .
git commit -m "feat: ..."
git push origin main
# → Cloudflare Pages 自動建置完成
```

---

## ⚙️ 本地開發設定

```bash
# 1. 安裝依賴
npm install

# 2. 登入 clasp
clasp login

# 3. 修改後端後推送
clasp push

# 4. 修改前端後直接 git push（Cloudflare 自動部署）
```

---

<div align="center">

**Built with ⚡ by [NiaodaoKu](https://github.com/NiaodaoKu)**

*$0 infra · Serverless · PWA · Multi-user · SRS*

</div>
