# News Recommendation Systems
## Executive Summary:
本文結合機器學習和神經網路的演算法，透過平台上使用者的歷史瀏覽紀錄和新聞內容推薦使用者感興趣的新聞。使用的演算法包括k-means、KNN和Bert模型(2018)，分析過程也有應用自然語言處理(NLP)相關的技術，如average word vectors、Doc2Vec、bag of words、TF-IDF和ELMo等將文章向量化的方法。
新聞的主要目的之一就是傳遞資訊，讓社會大眾能快速了解自己生活圈以外的事，而推薦系統就是希望在資訊繁雜且選擇多樣的情況，能準確的提供使用者感興趣的選擇標的。因此本文將推薦系統應用在財經新聞平台(MoneyDJ理財網)上。我們主要將推薦方法分為熱門度推薦、相似使用者推薦和個人化推薦。
•	熱門度推薦: 透過新聞標題與內文預測新聞會不會變熱門，來優先曝光最新且模型判定會變熱門的新聞。
•	相似使用者推薦: 透過過去使用者觀看新聞的產業標籤統計資料進行分群，並在不同群中找出點擊紀錄相近的使用者，找到目標使用者沒有看過，但相似使用者有看過的新聞進行推薦。
•	個人化推薦: 透過NLP的技術將使用者過去看過的新聞轉成向量，並當作使用者的新聞偏好，最後透過向量的相似性，找出與使用者偏好相近的新聞。

本文目的在於透過圖示的方式解釋如何應用人工智慧的技術，了解使用者在平台上的偏好和特性，以達到自動化精準推薦新聞的目的，下一頁展示了新聞推薦系統的推薦流程和當中應用的技術。
建構推薦系統的資料集合包含使用者行為資料(User_ Actions)、新聞資料集(News_info)和登入環境(ENV)，其相關變數列於下表當中。
