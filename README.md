# AI-Travel-Insurance-Specialist
📖 專案背景與目標**  
在投保海外旅平險時，使用者常面臨長達數十頁的 PDF 條款，內含艱澀的法律術語與複雜的理賠邏輯。本專案旨在開發一個 AI 旅平險專員，透過 RAG (Retrieval-Augmented Generation) 架構，讓使用者能以自然語言提問，並獲得準確、即時且具備法條依據的解答。  

**🛠️ AI 工具鏈與系統環境 (Toolchain & Environment)**  
本專案採用全棧 AI 鏈結與 Orchestration 能力建置，使用以下核心技術：  
開發環境：Google Colab (py 3.10)  
LLM 模型：Google Gemini API (具備動態模型探索機制，自動配對支援 generateContent 的 gemini-pro 或 **gemini-1.5-flash** 模型)    
檢索與框架：LangChain    
向量嵌入 (Embedding)：HuggingFace (shibing624/text2vec-base-chinese)  
向量資料庫 (Vector DB)：ChromaDB (本地端持久化儲存)  
資料前處理：正規化純文字檔 (旅平險保單合訂本 .txt) 搭配 Regex 正則表達式分塊。  

**RAG 核心參數須知 (finally.ipynb)**  
文字切片策略 (Text Splitting)：  
Chunk Size: 600 - 800 字（適用於法規條文）。  
Chunk Overlap: 10% - 15%（防止關鍵金額與條件被強行切分）。  
檢索權重 (Retrieval)：  
Top-K: 設定為 4，以獲取包含定義、範圍與除外責任的完整資訊。  
模型行為控制：  
Temperature: 0.0（確保輸出嚴謹且具一致性）。  
Prompt: 限制模型僅依據文本回答，嚴禁產生虛構的理賠規則。  

**環境與安全性須知**
API 金鑰：請使用 os.environ 讀取金鑰，嚴禁將 API Key 直接寫在代碼中上傳至版本控制系統。  
Rate Limiting：使用免費版 Gemini API Key 時，需在大量數據向量化過程中加入適當延遲 (Sleep)，避免觸發頻率限制。  

**🛠️操作與對話須知 (Usage Instructions)**  
啟動成功後，畫面會出現 👤 你的問題： 的輸入框。    
輸入你想詢問的旅平險情境（例如：「班機延誤了 5 小時怎麼賠？」或「只有一人食物中毒有賠嗎？」）。    
系統檢索後，會給出嚴格防幻覺的專業回答，並在底部印出**「📚 本次回答參考的唯一底層文獻」**。  
若要結束系統，請在輸入框輸入 q、quit 或 退出。  
