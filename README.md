# WeChat Clone Master Server（專案說明）

本倉庫是 **AltStore / SideStore 軟體源** 的中繼站：由本機 Master Server 完成打包與上傳後，GitHub 上會出現 `repo.json` 與 Release 中的 IPA，手機在同一 Wi‑Fi 下可透過 SideStore 同步更新。

> **授權提醒**：微信為騰訊產品。請僅使用你**有權取得與使用**的 IPA，並自行承擔帳號與合規風險。本 README 只描述架構與倉庫用途，不提供取得或修改官方客戶端的指引。

## 架構（三層）

```
[ 本機 Master Server ]
  → 取得 IPA（線上源或本目錄 input.ipa）
  → 安全掃描 .dylib、修改 Bundle ID、重打包
  → GitHub Release + 更新 repo.json
        ↓
[ GitHub Raw：repo.json ]
        ↓
[ iPhone：AltStore / SideStore ]
```

## 倉庫裡應有什麼（跑通一次部署後）

| 路徑 | 說明 |
|------|------|
| `repo.json` | SideStore / AltStore 訂閱用的源清單（由腳本生成） |
| `template.json` | 生成 `repo.json` 的模板（已納入版本庫） |
| GitHub Releases | 例如 `WeChat_Clone.ipa` 的公開下載位址 |

目前遠端倉庫若只有本 README，代表 **Master Server 尚未成功完成 Step 4–5**，或尚未推送。

## 本機 Master Server 檔案

可執行腳本（`master_daemon.py`）與日誌在你本機的 **server 壓縮包** 中維護，**未**放入此 Git 倉庫，以避免誤提交大型 IPA 或 Token。

建議本機目錄結構：

```
wechat-clone-master-server/
├── master_daemon.py   # v5 建議：支援同目錄 input.ipa 略過線上抓取
├── template.json      # 可與本 repo 根目錄同步
├── input.ipa          # 可選；>50MB 時 v5 會優先使用（勿 commit）
├── daemon_error.log
└── requirements.txt   # 見本 repo
```

### v4 與 v5 差異（摘要）

- **v4**：僅線上抓取 IPA；失敗即退出（常見：`Failed to fetch any valid WeChat IPA download link`）。
- **v5**：若已有 `input.ipa` 則跳過線上抓取；失敗時提示改用手動 IPA；完成後保留 `input.ipa`。

`template.json` 在 v4/v5 間內容一致。

## SideStore 訂閱 URL

部署成功後，在 SideStore 新增源：

```
https://raw.githubusercontent.com/<你的 GitHub 用戶名>/wechat-clone-master/main/repo.json
```

例如：`chansham` 帳號下為  
`https://raw.githubusercontent.com/chansham/wechat-clone-master/main/repo.json`

## 依賴

```bash
pip install -r requirements.txt
```

## 常見問題

### Step 2 線上抓取失敗

公開 Telegram / GitHub 備援源可能失效、被限流或因地區無法存取。v5 做法：在腳本同目錄放置合法取得的 `input.ipa` 後重跑。

### GitHub API 錯誤

- Personal Access Token 需 **`repo`**（公開倉庫）權限。
- 403 / 401：檢查 Token 是否過期、是否誤用 fine-grained 權限不足。

### 倉庫已存在但沒有 repo.json

屬正常中間狀態：Step 1 通過後在 Step 2–3 失敗，不會寫入源文件。

## 維護節奏

- 微信新版本：本機執行一次 Master Server（或排程），確認 Release 與 `repo.json` 已更新。
- 手機：連上與電腦相同 Wi‑Fi，SideStore 會依源設定背景檢查更新。

## 相關連結

- 本倉庫：<https://github.com/chansham/wechat-clone-master>
