# 🤖 AI 旅平險專員：智慧化旅遊平安險文件檢索系統 (RAG-based Insurance Specialist)

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![Framework-LangChain](https://img.shields.io/badge/Framework-LangChain-green.svg)](https://github.com/langchain-ai/langchain)
[![LLM-Gemini_Flash](https://img.shields.io/badge/LLM-Gemini_Flash_1.5-orange.svg)](https://deepmind.google/technologies/gemini/)

本專案為**生成式 AI 期末分組報告**之研究成果。我們建立了一個專門針對「旅遊平安險」的文件檢索系統（RAG），旨在讓使用者在投保前能徹底了解條款疑慮。系統定位為**「專屬 AI 保險專員」**，提供精確且具備法規實證的解答，有效避免保險理賠爭議。

---

## 🎯 系統核心亮點
- **100% 可溯源性**：拋棄傳統固定字數切塊（Fixed-size Chunking），改用**語意結構切塊**，精準追蹤至「第 X 條」。
- **嚴謹的邊界控制**：透過精準提示詞工程，徹底**消除通識污染（General Knowledge Contamination）**，拒絕給出似是而非的通識回答。
- **高效本地向量推理**：結合開源中文 Embedding 模型與 NVIDIA T4 GPU 算力，保障高水準的中文語意檢索表現。

---

## 📂 系統環境與開發工具 (Environment & IDE)
為了符合評分標準與執行紀錄要求，本系統之建置環境如下：
* **執行平台**：Google Colab
* **算力資源**：NVIDIA T4 GPU（用於本地端 HuggingFace Embedding 模型推理加速）
* **輔助 IDE**：Jupyter Notebook（用於主要開發、測試、視覺化 DataFrame 與驗證檢索結果）

---

## 🛠️ AI 工具鏈整合與技術棧 (Tech Stack)
本專案透過 **AI Orchestration (AI 編排)** 能力，串聯了以下前沿開源與雲端工具：

| 組件 | 技術/工具 | 說明 |
| :--- | :--- | :--- |
| **語言模型 (LLM)** | Google Generative AI | 使用 **Gemini Flash 1.5**，負責最終的語意理解與對話生成。 |
| **編排框架** | LangChain | 整合 `langchain_core` 與 `langchain_google_genai`，串接資料流、向量庫與 LLM。 |
| **語意向量 (Embedding)** | HuggingFace | 採用開源 **`shibing624/text2vec-base-chinese`**，將中文條款轉換為精確向量。 |
| **向量資料庫** | ChromaDB | 高效儲存與檢索 **336 筆** 精準切分之保單條款區塊。 |
| **文件解析工具** | pdfplumber + re | 搭配 Python 正規表達式（Regular Expression）進行文本抽取與進階清洗。 |

---

## ⚙️ 系統完整設計流程 (System Design Flow)

### 📌 A. 資料收集 (Data Collection)
* **資料來源**：國泰產物旅平險保單條款、南山產物旅平險保單條款。
* **資料解析與清洗**：透過 Python 的 pdfplumber 套件讀取文件內容。針對保險合約中非結構化的特性，設計了 process_insurance_pdf 函數進行資料清洗（例如使用正規表達式清除多餘換行，或將文件中如「樣張」之類的浮水印與雜訊過濾掉） 
  
### 📌 B. 系統建置 (System Build)
1. **精準分塊 (Chunking Strategy)**：
   為解決法律/保險術語的複雜度與結構完整性，本系統捨棄固定字數裁切，改用 `re.split` 依**「第 X 條」**標題進行條文切塊。同時，將「保險公司名稱」與「條款標題」作為 **Metadata** 綁定於每個區塊，確保回答時 100% 可溯源。   
2. **語意向量化 (Embedding)**：引入 Hugging Face 的開源模型 shibing624/text2vec-base-chinese，將清洗好的條款轉換為高維度的語意向量，用以精確捕捉保險條款中嚴謹的語意歧義。
3. **向量資料庫建置**:將轉換後的向量與對應的 Metadata，統一存入 Chroma 向量資料庫中。  
4. **提示詞工程 (Prompt Engineering)**：
   設計嚴格的限制性提示詞，強制 AI 必須完全基於參考資料回答。若檢索範圍內無答案，則需誠實回報「無法得知」。系統設定抓取最相關的 8 個條文區塊 ($k=8$) 來應對複雜的交叉條款查詢。 

### 📌 C. 驗證與對比分析 (Validation & Analysis)
我們將本系統（RAG）與前沿通用 AI（如 Perplexity）進行了基準測試與對比分析：

* **消除通識污染**：在測試「燒燙傷定額給付」時，Perplexity 會產生「看似專業的通識覆蓋」，誤稱南山產物有相同理賠；而本系統 (RAG) 能精確回報南山條款未提及該項目，展現了嚴謹的邊界控制。
* **正確性優先**：通用 AI 傾向使用機率性詞彙（如「多半會認定」、「通常會理賠」），而本系統能直接提供如 **`[南山] - 第二十五條`** 等精確出處，能有效避免實務上的理賠爭議。

---

