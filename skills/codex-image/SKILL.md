---
name: codex-image
description: 透過 Codex CLI 內建的影像生成工具產生插圖，並把原圖存到目前專案的資料夾。使用者說「用 Codex 產圖」「幫我畫一張圖」「生成插圖」時使用。
---

# codex-image：經 Codex CLI 產圖

## 產圖前（一律）

1. **先交方案、等使用者說「開始」才跑**：說明用途、題材、尺寸、風格、張數。每張圖都會用掉使用者 ChatGPT 帳號的額度。
2. **一次指令只產一張**；要多張就逐張呼叫，事先說明總張數。
3. 依「提示詞規則」檢查提示詞，有問題先提出。

## 提示詞規則

不得寫進提示詞，也不得附檔：
- 真實人物的姓名、個資、聯絡方式，以及可辨識的學校、機構或社區名稱。
- 機密或未公開的內容。
- **真實照片作為參考圖**，尤其是有人臉的照片，一律不上傳。使用者逐張明確指定的除外，且須先確認照片已取得授權。

建議寫法：
- 要求「圖中不出現任何文字、招牌、標誌或 logo」，因為模型畫出的文字常有錯；標題請另外加。
- 人物卡通化、不畫可辨識的真實面孔。
- 指定橫式或直式，以及比例；構圖預留標題位置。

## 執行指令

工作資料夾預設為目前專案下的 `ai_images/`。

```bash
mkdir -p ai_images && cd ai_images && codex exec -C "$PWD" -s workspace-write -o _last_message.txt \
"請使用你的影像生成（image generation）工具產生一張圖，需求如下：
<提示詞>
尺寸／比例：<例如 1024x1024、橫式 16:9、直式 9:16>。
規則：
1. 工具產出的原始檔（在 ~/.codex/generated_images/ 底下）以 cp 原樣複製到目前工作目錄，檔名 <檔名>.png；不縮放、不重新編碼、不用程式繪圖替代。
2. 若影像生成工具不可用或失敗，明確說明原因並停止。
3. 完成後只回覆：存檔絕對路徑、像素尺寸、檔案大小。"
```

要點：
- `-s workspace-write` 一定要加，否則 Codex 不能寫檔；`-C` 把寫入範圍限制在工作資料夾。
- **不得加** `--dangerously-bypass-approvals-and-sandbox`。
- 每張約 1–2 分鐘。實際尺寸由模型決定，可能和要求略有出入。

## 產圖後（一律）

1. 用 Read 打開圖片，目視整張圖：有沒有出現文字、有沒有不當內容，以及模型自己加進去的元素。
2. 回報：檔案路徑、像素尺寸、檔案大小。
3. 要修改時，檔名依序改成 v2、v3，不覆寫前一版。
4. 提醒使用者：這是 AI 生成圖，用在作業、海報或簡報時，請在圖說標示「AI 生成插圖」，也不要當作真實照片使用。

## 不做的事

- 不代為執行 `codex login`／`codex logout`，不讀取或修改 `~/.codex/auth.json`、`~/.codex/config.toml`。
- 遇到連線或憑證錯誤時，不關沙箱、不設定 `SSL_CERT_FILE`／`--cacert` 去信任來路不明的憑證，也不改 VPN 或防毒設定；只做「排障」節的唯讀檢查並回報，由使用者處理。
- 不自動清理 `~/.codex/generated_images/`；要清理時先列出清單，經使用者同意才刪。

## 排障

| 現象 | 可能原因 | 唯讀檢查 | 由使用者處理 |
|---|---|---|---|
| `codex: command not found` | 未安裝，或 npm 全域路徑不在 PATH | `npm -v`、`npm root -g` | 重新安裝；必要時重開終端機 |
| `Not logged in` | 尚未登入或登入已過期 | `codex login status` | 自己執行 `codex login` |
| `image_generation` 不是 `true` | 版本太舊，或帳號方案不支援 | `codex --version` | `npm install -g @openai/codex` 更新；若仍不行，可能是帳號方案不支援 |
| `invalid peer certificate: UnknownIssuer`，或 `self signed certificate in certificate chain`（**瀏覽器卻正常**） | 防毒軟體的「加密連線掃描」、公司或學校的網路代理、VPN 對 HTTPS 做中間人檢查。瀏覽器信任那張根憑證，Codex 不信任 | 見下方指令，看簽發者是不是 Google Trust Services 等公認 CA | 在防毒軟體把 `chatgpt.com` 加入受信任網址；或換一個網路（例如手機熱點）；或暫時關閉 VPN |
| 反覆重連後 `stream disconnected` | 同上，或網路不穩 | 同上，再做最小連線測試 | 同上 |
| 額度用完的訊息 | 帳號的產圖額度已用完 | 無 | 等額度重置 |

檢查憑證簽發者：

```bash
echo | openssl s_client -connect chatgpt.com:443 -servername chatgpt.com 2>/dev/null | openssl x509 -noout -issuer
```

最小連線測試（不產圖，只耗用少量額度）：

```bash
codex exec --skip-git-repo-check -s read-only "只回覆兩個字：OK"
```

回報格式：現象一句、上述檢查的結果、建議使用者採取哪一項處置。**不要為了「再試一次」反覆消耗額度。**
