# TC-JetBrainsMapleMono 字型更新工具

Windows 一鍵安裝／更新 [TC-JetBrainsMapleMono](https://github.com/bgtsai/TC-JetBrainsMapleMono) 簡轉繁字型的小工具。

字型會隨上游改版或保留字調整而推出新版。手動更新要先移除舊字型、下載、解壓、再一個個安裝，很繁瑣。如果你常常需要更新，可以用這個工具一鍵完成。

**[下載最新版 TC-JBMM-Updater.zip](https://github.com/bgtsai/TC-JetBrainsMapleMono-Updater/releases/latest/download/TC-JBMM-Updater.zip)**（Windows 10／11）　·　[所有版本](https://github.com/bgtsai/TC-JetBrainsMapleMono-Updater/releases)

## 它會做什麼

按一下，工具會依序：

1. 讀取設定檔，確認你要裝哪些代碼、哪些字重
2. 查詢 GitHub 上的最新版本；跟上次安裝的版本與設定都一樣時，直接顯示「已是最新」，不重複下載
3. 下載需要的 zip（每個檔案都有逾時與自動重試，並核對檔案大小）並解壓
4. 移除這些代碼在 Windows 裡的舊字型（系統與個人兩個字型資料夾都會清）
5. 安裝新字型給這台電腦的所有使用者，並通知系統字型已更新

## 使用步驟

1. 下載上面的 zip，解壓到任意資料夾（例如 `D:\Tools\TC-JBMM-Updater`），`TC-JBMM-Updater` 資料夾裡的三個檔案要放在一起：
   - `更新TC_JBMM字型.bat`：雙擊執行的入口
   - `Update-TCJBMM.ps1`：工具本體
   - `TC-JBMM-Updater.ini`：設定檔
2. 用記事本打開 `TC-JBMM-Updater.ini`，改成你要的代碼與字重（說明見下一節）
3. 雙擊 `更新TC_JBMM字型.bat`，Windows 跳出「使用者帳戶控制」時按「是」（安裝給所有使用者需要系統管理員權限）
4. 等待完成。之後想更新時，再雙擊一次就好

## 設定檔說明

```ini
[General]
; 要抓哪一版：latest＝正式版（建議）；pre＝開發版（更新頻繁）
Tag = latest

[Fonts]
; 格式：代碼 = 字重清單（用逗號分隔），* 代表全部 16 個字重
1011 = *
1111 = Regular, Bold, Italic, BoldItalic
```

- **`[General]` 區段的 `Tag`**：`latest` 是正式版，跟著上游正式 release 更新，建議一般使用。`pre` 是開發版，上游每次 commit 都會更新，比較頻繁。
- **`[Fonts]` 區段**：每行一組，`代碼 = 字重`。
  - 代碼是字型 zip 檔名裡的 4 位數（例如 `TC-JBMM-1011.zip` 的 `1011`），每一位代表一個特性開關，見下方「代碼對照」。
  - 字重可以寫 `*`（全部 16 個），也可以列出要的字重，用逗號分隔，大小寫不拘。
  - 可用的字重：`Thin`、`ThinItalic`、`ExtraLight`、`ExtraLightItalic`、`Light`、`LightItalic`、`Regular`、`Italic`、`Medium`、`MediumItalic`、`SemiBold`、`SemiBoldItalic`、`Bold`、`BoldItalic`、`ExtraBold`、`ExtraBoldItalic`
- 分號 `;` 或井號 `#` 開頭的行是註解。

## 常見設定範例

| 想要的效果 | 寫法 |
|---|---|
| 整組全部安裝 | `1011 = *` |
| 只裝常用的 4 個字重（一般、粗體、斜體、粗斜體） | `1111 = Regular, Bold, Italic, BoldItalic` |
| 只裝單一字重 | `0000 = Regular` |
| 同時裝好幾組 | 每組各寫一行，例如 `1011 = *` 和 `0000 = Regular` |
| 暫時不要裝某一組 | 在那一行前面加 `;` 註解掉 |

設定改了之後，下次執行就會照新的設定重裝，不必等字型出新版。如果某一組裡拿掉了幾個字重，那幾個字重會被移除。

設定檔裡**沒有列出**的代碼，工具不會去動它。例如你以前手動裝過 `0000`，設定檔沒寫 `0000`，它就會保持原樣。

設定檔寫錯時（例如代碼不是 4 位、字重拼錯），工具會列出是第幾行、哪裡錯，並停下來，不會裝到一半。

## 進階用法

在 `更新TC_JBMM字型.bat` 後面加參數（例如建一個捷徑，在「目標」欄位後面加）：

| 參數 | 作用 |
|---|---|
| `-Force` | 版本沒變也強制重新安裝 |
| `-Tag pre` | 這次改抓開發版（優先於設定檔裡的 `Tag`） |
| `-Config 路徑` | 改用另一份設定檔 |

## 常見問題

- **為什麼要按「是」？** 安裝字型給所有使用者，必須寫入 `C:\Windows\Fonts`，Windows 規定這需要系統管理員權限。這個確認視窗是 Windows 的安全機制，沒有辦法跳過。
- **顯示「有舊字型正在被程式使用」？** 有程式（例如開著的編輯器）正在使用舊字型，檔案被鎖住刪不掉。新字型已經裝好了，舊檔案會在重新開機後自動刪除。
- **裝好了，編輯器卻還是舊字型？** 已經開著的程式要重新啟動，才會讀到新字型。
- **有裝 Adobe Acrobat？** 工具在安裝期間會暫停 Acrobat 的常駐程式（acrotray），裝完再恢復，避免它收到字型變更通知時叫起 Distiller，跳出「無法建立暫存資料夾」的錯誤。
- **電腦上的狀態記錄在哪裡？** `C:\ProgramData\TC_JBMM_Updater\`，刪掉的話，下次執行就會重新安裝。

## 代碼對照

字型 zip 的 4 位代碼依序對應 4 個特性開關，**1 = 啟用、0 = 停用**：

| 位數 | 特性 | 說明 |
|---|---|---|
| 第1位 | NF（Nerd Font） | 為部分開發工具、命令列終端機、程式碼編輯器等提供圖示支援（會導致字型檔案體積略微增大） |
| 第2位 | NR（CN Narrow） | 縮小中日字型間距（會導致中英文/日英文不再 2:1 寬完美對齊） |
| 第3位 | NL（No Ligatures） | 1 表示停用連字；0 表示保留連字 |
| 第4位 | HT（Hinted） | 使字型在低解析度螢幕上（≤1080P）的渲染更加均勻（可能會導致字型在高解析度螢幕上的渲染略微模糊） |

例如 `1011` = 有 Nerd Font 圖示、不縮窄中文間距、停用連字、有 Hinting。各特性的詳細說明與 16 種組合的完整對照表，見 [TC-JetBrainsMapleMono 的 README](https://github.com/bgtsai/TC-JetBrainsMapleMono#下載使用)。

## 相關專案

- [TC-JetBrainsMapleMono](https://github.com/bgtsai/TC-JetBrainsMapleMono)：本工具下載的字型來源，自動把 Fusion-JetBrainsMapleMono 轉成簡轉繁字型
- [TCFontCreator](https://github.com/bgtsai/TCFontCreator)：字型簡轉繁使用的轉換工具

## 授權

本工具的程式碼以 [MIT License](LICENSE) 授權。下載安裝的字型依 TC-JetBrainsMapleMono 的 SIL Open Font License 1.1 授權。
