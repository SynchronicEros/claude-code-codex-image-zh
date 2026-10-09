# codex-image（經 Codex CLI 產圖）

讓 Claude Code 呼叫 **Codex CLI 內建的影像生成工具**產圖，原圖直接存進目前專案的 `ai_images/`。這是一個 skill，不是 Mod：裝好後對 Claude 說「幫我用 Codex 產一張圖：……」就會觸發。

## 安裝

**需要 Claude Code（付費方案）；Codex 免費版不能安裝。**本 skill 裝在 Claude Code 裡，再由它呼叫 Codex CLI 產圖。

使用前須先完成下方「前置條件」（安裝並登入 Codex CLI）。

```bash
claude plugin marketplace add SynchronicEros/claude-code-codex-image-zh
```

```bash
claude plugin install codex-image@claude-code-codex-image-zh
```

安裝或更新後，**新開的 session 才會生效**。全部 Mod 與 skill 一起管理，見總目錄 [claude-code-mods-zh](https://github.com/SynchronicEros/claude-code-mods-zh)。

## 前置條件（自己做，不要交給 Claude 代做）

1. 安裝 Node.js（LTS 版），再安裝 Codex CLI：

   ```bash
   npm install -g @openai/codex
   ```

2. 由坐在這台電腦前的人自己登入 ChatGPT：

   ```bash
   codex login
   ```

3. 檢查：`codex login status` 應顯示 `Logged in using ChatGPT`，`codex features list` 中的 `image_generation` 應為 `true`。

Windows 上的 Claude Code 需要 Git Bash。

## 運作方式

- Claude 會先說明用途、題材、尺寸、風格與張數，**等你回「開始」才產圖**；一次指令只產一張。
- 以 `codex exec -s workspace-write` 呼叫產圖工具，要求 Codex 把原始檔原樣 `cp` 到工作資料夾（不縮放、不重新編碼）。
- 產完會打開圖片目視檢查，回報路徑、尺寸與大小；改版時檔名依序改成 v2、v3。

## 額度與隱私

- 每張圖都會用掉登入帳號的 ChatGPT 產圖額度，每張約 1–2 分鐘。
- **請用自己的 ChatGPT 帳號**，不要多人共用：共用違反 OpenAI 使用條款，對話紀錄也會互相看得到。
- skill 規定不把真實人物的姓名、個資或照片放進提示詞；AI 生成的圖在作業、海報中使用時，請標示「AI 生成插圖」。
- Claude 不代為登入，也不讀取 `~/.codex/auth.json`。遇到憑證錯誤時不會關沙箱或改防毒、VPN 設定，只做唯讀檢查後請你處理（排障表見 skill 內文）。

## 授權

MIT（見 [LICENSE](LICENSE)）。

---

**English:** A skill that lets Claude Code generate images through the Codex CLI's built-in image tool and copy the original file into `ai_images/` in the current project. Prerequisites (do these yourself): `npm install -g @openai/codex`, then `codex login`. Claude proposes the image first and waits for your go-ahead; one image per call. Each image uses your own ChatGPT quota; do not share ChatGPT accounts. On Windows, Claude Code needs Git Bash.

**Install / License (English):** Requires Claude Code (a paid plan); the free Codex tier cannot install it. Complete the prerequisites below first. `claude plugin marketplace add SynchronicEros/claude-code-codex-image-zh`, then `claude plugin install codex-image@claude-code-codex-image-zh`; takes effect in new sessions. All mods and skills: [claude-code-mods-zh](https://github.com/SynchronicEros/claude-code-mods-zh). MIT.
