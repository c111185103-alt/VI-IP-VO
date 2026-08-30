# Issue：新版圖表（Breakdown / 方塊圖 / AOV / TSPEC）是否比 `HDMI_DATAFLOW.md` 更精確？

## 結論

**是，而且是具體、可指認的幾個地方更精確，不是泛泛而談的「畫得比較好看」。** 逐項列在下面，每一項都標明「新增的正確細節」還是「新增的分析層次」，而不是含糊地說「新版比較好」。

**更新（術語修正）**：文中/圖中原本泛稱的「IP」（例如「19顆IP」「可編輯IP」），依論文定義應正名為 **SIP**（矽智財，Silicon Intellectual Property——可獨立整合與驗證、有明確介面的硬體模組）。論文裡的「IP」專指 `VI–IP–VO` 管線中「處理」這個**架構角色**，可以由 0、1 或多顆 SIP 填入，不是元件種類，兩者不能混用。詳見 `# CLAUDE.md` 的術語辨正。本文與 `Breakdown.drawio` 已依此更新，`AOV-A`/`AOV-B` 裡的 `user_ip` 綠色節點不受影響（那本來就是論文自己的角色名稱）。

---

## 1. 系統總覽：`方塊圖_電路圖.drawio` vs `HDMI_DATAFLOW.md` 圖 1

[![方塊圖_電路圖](image/方塊圖_電路圖.drawio.png)](image/方塊圖_電路圖.drawio.png)
*（圖較大，點圖看原始解析度；或開啟 [方塊圖_電路圖.drawio](方塊圖_電路圖.drawio) 用 draw.io 無限縮放）*

**新增的正確細節：`hdmi_out_ddc` 是 PS7 的 `IIC_0` 直接接出去的，不經過任何 FPGA 邏輯。**
這是對照 `design_1_bd.tcl` 第 808 行 `processing_system7_0_IIC_0` 這條連線才發現的，`HDMI_DATAFLOW.md` 原本的 Mermaid 圖完全沒有畫出這條線——原圖只畫了控制面對 5 顆 SIP 的 AXI-Lite 扇出，沒有畫 PS7 這條額外的直連 I2C。這不是風格差異，是原圖漏掉了一條真實存在的連線。

**新增的正確層次：中斷彙整線路重新合併回同一張圖。**
`HDMI_DATAFLOW.md` 因為 Mermaid 沒有「跳線」功能，中斷彙整（`xlconcat_0`）被迫拆成獨立的「圖 2b」，讀者要對照兩張圖才能理解中斷怎麼跟控制/資料面互動。`方塊圖_電路圖.drawio` 用 draw.io 原生的 `jumpStyle=arc` 把交叉的線做跳線處理，中斷、AXI-Lite 控制、資料面三種線可以合併回**一張完整的圖**，資訊沒有變多，但完整性跟可讀性都提升了。

---

## 2. 元件總表：`Breakdown.drawio` vs `HDMI_DATAFLOW.md` 的「SIP 節點總表」

[![Breakdown](image/Breakdown.drawio.png)](image/Breakdown.drawio.png)
*（這張最大，原圖 16384×1557px，強烈建議點圖看原始解析度；或開啟 [Breakdown.drawio](Breakdown.drawio) 用 draw.io 無限縮放）*

**新增的分析層次：3 顆可編輯 SIP 的內部子模組，原本的文字表沒有列到這麼深。**
`HDMI_DATAFLOW.md` 的 SIP 節點總表只列到 19 顆頂層 SIP 這一層（例如「`dvi2rgb_1` — TMDS 解碼器」），沒有再往下拆。`Breakdown.drawio` 多了第三層，把 `dvi2rgb_1`／`rgb2dvi_0`／`axi_dynclk_0` 底下實際的原始碼檔案（`TMDS_Clocking`、`InputSERDES+ChannelBond+PhaseAlign`、`mmcme2_drp+BUFIO/BUFR` 等）都畫成子節點，這些檔名跟角色是我們逐行讀過 `axi_dynclk.vhd`/`axi_dynclk_S00_AXI.vhd` 之後才寫得出來的，原本的文字表完全沒有這個深度。

**新增的正確標記：可編輯 vs 廠商封裝 SIP 的視覺區分（實線／虛線），以及資料路徑 vs 控制旁支的標記。**
這兩個維度原本只存在於 `HDMI_DATAFLOW.md` 的**文字敘述**裡（例如「只有 3 顆可編輯」這句話），不是圖上可以直接看出來的視覺屬性。`Breakdown.drawio` 把「能不能編輯」做成實線/虛線、「是不是真的承載像素資料」做成〔資料路徑〕/〔控制‧狀態旁支〕標籤，直接畫在每個節點上，不用再回頭讀文字才能確認。

---

## 3. AOV-A / AOV-B / TSPEC-A：`HDMI_DATAFLOW.md` 完全沒有對應內容

[![AOV-A](image/AOV-A_直接顯示模式.drawio.png)](image/AOV-A_直接顯示模式.drawio.png)
*（點圖看原始解析度；或開啟 [AOV-A_直接顯示模式.drawio](AOV-A_直接顯示模式.drawio)）*

[![AOV-B](image/AOV-B_CPU影像處理模式.drawio.png)](image/AOV-B_CPU影像處理模式.drawio.png)
*（點圖看原始解析度；或開啟 [AOV-B_CPU影像處理模式.drawio](AOV-B_CPU影像處理模式.drawio)）*

[![TSPEC-A](image/TSPEC-A_直接顯示模式.drawio.png)](image/TSPEC-A_直接顯示模式.drawio.png)
*（點圖看原始解析度；或開啟 [TSPEC-A_直接顯示模式.drawio](TSPEC-A_直接顯示模式.drawio)）*

這三張不是「把舊圖畫更準」，是**全新的分析層**，`HDMI_DATAFLOW.md` 裡沒有任何對應的東西可以比較：

- AOV-A/B 是照論文（`電腦視覺加速系統晶片之階層式記憶體架構多目標最佳化`）的 VI–IP–VO 形式化規則畫的，把這個 HDMI 專案精確歸類成「VI=外部串流型、VO=固定時序顯示型、預設模式無 IP、選單7/8 由軟體填 IP 角色」，並誠實標出這個設計跟論文通用模型的三個差異（沒有 Layer-2 可調式緩衝器、D1=D2 共用同一組 DDR、軟體 IP 沒有 slice/merge）。
- TSPEC-A 第一次把系統的**真實時序數字**攤開來看：`T_line=32µs`、`T_active=25.6µs`、`T_burst=240ns`（來自 `.bd.tcl` 裡 `PCW_ACT_FPGA1_PERIPHERAL_FREQMHZ=133.333344` 這個實際合成後的數字，不是隨口說的「約100MHz」）、每行剛好整除成 15 個 burst。`HDMI_DATAFLOW.md` 從頭到尾沒有出現過任何一個時序數字，都是文字描述「快」「慢」「同時」。

---

## 為什麼沒有 `TSPEC-B_CPU影像處理模式`

這不是漏做，是**刻意先跳過**，原因記錄在這裡避免以後忘記：

TSPEC-A 的每一個時間數字（`T_line`、`T_burst`……）都能用公式 3~8 配合 `.bd.tcl` 裡的真實合成參數（解析度、AXI 寬度、`f_sys=133.333MHz`、burst length）直接算出來，屬於論文說的 Loop 1 分析式推算，符合論文「數字不能用猜的」這條規則。

但 TSPEC-B 需要多一個數字：`user_ip`（`DemoInvertFrame`/`DemoScaleFrame`）每個像素花多少 ARM 週期（公式 7 的 `t_pix`）。這個數字**沒有對應公式可以推算**——ARM 執行時間不像 AXI burst 有固定的頻寬公式，只能靠實際跑在硬體上量出來（例如在 `video_demo.c` 裡用 `SCUTIMER` 在 `DemoInvertFrame`/`DemoScaleFrame` 前後各讀一次計時器）。目前沒有這個實測數字，硬湊一個估計值會違反論文自己訂的「不能用推算或估計值」這條規則，所以選擇先不畫 TSPEC-B，等真的量到 `t_pix` 之後再補（`AOV-B_CPU影像處理模式.drawio` 圖上的備註也寫了同一件事）。

---

## 小結

| 檔案 | 相對 `HDMI_DATAFLOW.md` 的關係 |
| --- | --- |
| `方塊圖_電路圖.drawio` | 修正遺漏（PS7 直連 DDC）+ 合併回單一完整圖 |
| `Breakdown.drawio` | 補上原本只到頂層的深度（3 顆可編輯 SIP 的內部子模組）+ 把文字敘述做成可視化標記 |
| `AOV-A`/`AOV-B` | 全新分析層，套用論文的形式化規則 |
| `TSPEC-A` | 全新分析層，第一次給出真實時序數字 |
| `TSPEC-B` | 尚未產生，卡在缺一個需要實測才能取得的數字（`t_pix`） |

`HDMI_DATAFLOW.md` 本身沒有錯誤需要撤回，它的文字說明（中斷鏈步驟、UART 選單對照表）仍然是目前最完整的敘述版本；新的這幾張圖是在它之上補精度、補分析深度，兩份文件互補，不是取代關係。
