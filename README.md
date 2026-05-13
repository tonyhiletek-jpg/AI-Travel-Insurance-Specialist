# AI-Travel-Insurance-Specialist
Insurance-RAG: 旅平險保單智慧諮詢助手技術文件
1. 專案執行架構與邏輯
本專案採用 RAG (Retrieval-Augmented Generation) 架構，將保險法律文本轉化為可互動的知識庫。主要執行流程如下：

掃描與優化 (Ingestion)：透過 txt.ipynb 解決 PDF 格式混亂、條文跨欄位與表格斷裂問題。
向量化 (Embedding)：將清理後的文本切割成帶有語意重疊 (Overlap) 的區塊，並儲存於 ChromaDB。
檢索與生成 (RAG)：透過 LangChain 接收使用者提問，檢索最相關的條文區塊，再由 Gemini 1.5 Flash 進行精確回答。
2. 數據處理須知 (txt.ipynb)
針對保險保單的高度非結構化特性，處理時須注意： * 佈局感知 (Layout Awareness)：使用 pdfplumber 偵測垂直邊界，防止雙欄排版導致的文字讀取順序混亂。 * 標籤化清理：利用正則表達式保留「第 X 條」作為檢索標記，並移除頁碼、頁首頁尾等無效資訊。 * 語意重建：合併受排版影響而斷裂的句子，確保法律定義的完整性。

3. RAG 核心參數須知 (finally.ipynb)
文字切片策略 (Text Splitting)：
Chunk Size: 600 - 800 字（適用於法規條文）。
Chunk Overlap: 10% - 15%（防止關鍵金額與條件被強行切分）。
檢索權重 (Retrieval)：
Top-K: 設定為 4，以獲取包含定義、範圍與除外責任的完整資訊。
模型行為控制：
Temperature: 0.0（確保輸出嚴謹且具一致性）。
Prompt: 限制模型僅依據文本回答，嚴禁產生虛構的理賠規則。
4. 環境與安全性須知
API 金鑰：請使用 os.environ 讀取金鑰，嚴禁將 API Key 直接寫在代碼中上傳至版本控制系統。
Rate Limiting：使用免費版 API 時，需在大量數據向量化過程中加入適當延遲 (Sleep)，避免觸發頻率限制。
5. 常見問題與排除 (Troubleshooting)
檢索不到條文：檢查原文 .txt 是否因 OCR 或 PDF 加密導致文字損毀。
回答偏離：優化 System Prompt，要求模型必須在回答最後標註「引用條文來源」。
