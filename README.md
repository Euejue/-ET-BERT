# ET-BERT 加密流量分析與惡意行為辨識實作

## 專案目的

本專案旨在驗證 **:contentReference[oaicite:0]{index=0}**
在 **Encrypted Traffic Analysis（ETA）** 任務中的可行性，
透過 **預訓練（Pre-train）與微調（Fine-tune）** 技術，
分析無法解密之網路流量，辨識遠端控制軟體與惡意攻擊行為。

實作重點聚焦於：

- **合法遠端工具 vs 惡意行為** 的區分
- **Ransomware 與 C2 行為特徵學習**
- **ET-BERT 與 LLM 分析能力比較**
- 驗證 AI 在「不解密流量」前提下的實務偵測能力

---

## 系統架構說明

### 流量資料來源（PCAP）

本實作使用多類 PCAP 流量樣本：

- TeamViewer 遠端連線
- AnyDesk 遠端控制
- Ransomware 加密流量
- **:contentReference[oaicite:1]{index=1}** Command & Control 通訊

所有流量皆保持 **TLS / 加密狀態**，不進行解密。

---

### 特徵擷取與資料處理

從 PCAP 中擷取下列特徵作為模型輸入：

- Packet Length Sequence
- Inter-arrival Time
- Direction（Inbound / Outbound）
- Flow-level Statistics

並將流量轉換為 ET-BERT 所需之 **Token Sequence 表示法**。

---

### 模型架構

#### ET-BERT（核心模型）

- 基於 Transformer Encoder
- 使用自監督預訓練學習通用加密流量語意
- 微調任務為多分類（Multi-class Classification）

分類標籤包含：

- Normal Encrypted Traffic
- TeamViewer
- AnyDesk
- Ransomware
- Cobalt Strike C2

---

### LLM 比較模型

為驗證 ET-BERT 的專業性，
本實作同步比較 **大型語言模型（LLM）** 在流量判斷上的能力：

- LLM 僅接收統計特徵描述（非封包序列）
- 無法直接理解封包時序語意
- 作為「通用 AI vs 專用模型」對照組

---

## 技術重點

- Encrypted Traffic Analysis（ETA）
- Self-Supervised Pre-training
- Fine-tuning for Traffic Classification
- Transformer-based Sequence Modeling
- PCAP → Flow → Token Pipeline
- 專用模型（ET-BERT） vs 通用模型（LLM）

---

## 實驗流程

### Step 1：資料準備

- 收集並標註 PCAP 流量
- 依應用與攻擊類型分類
- 切割為 Training / Validation / Test Set

---

### Step 2：ET-BERT 預訓練

- 使用未標註加密流量進行 Masked Token Prediction
- 學習流量行為的通用表示（Traffic Representation）

---

### Step 3：模型微調（Fine-tuning）

- 載入預訓練權重
- 針對五類流量進行分類訓練
- 使用 Cross-Entropy Loss

---

### Step 4：模型比較

| 模型 | 加密流量理解 | 行為時序 | 惡意 C2 辨識 |
|----|----|----|----|
| ET-BERT | ✔ | ✔ | ✔ |
| LLM | ✖ | ✖ | 部分 |

---

## 實驗結果摘要

- ET-BERT 能有效區分 **合法遠端工具與惡意 C2 行為**
- 對 Ransomware 與 Cobalt Strike 流量具高辨識率
- LLM 在高階描述分析尚可，但無法掌握封包時序語意
- 證實 **專用模型在 ETA 任務中具明顯優勢**

---

## 實務應用情境

- SOC 中的加密流量威脅偵測
- 無法解密環境下的側信道分析
- 與 SIEM / IDS（如 Wazuh）進行事件關聯
- 零信任（Zero Trust）環境中的行為型防禦

---

## 專案總結

本專案展示了 ET-BERT 在 **加密流量分析領域的實務價值**，
證明即使在 TLS 全面加密的環境中，
仍可透過 AI 模型學習流量行為模式，達成有效威脅辨識。

相較於通用型 LLM，
ET-BERT 在 **行為理解、時序建模與惡意流量辨識** 上具備顯著優勢，
適合用於實際企業級資安監控與威脅狩獵場景。
