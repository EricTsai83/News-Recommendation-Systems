以下 notebook 並未包含全部分析流程和運行環境，但為主要分析流程與架構

notebook: user_based_data
data:
final_final: user 過去點擊新聞資料(只包含歷史點擊記錄超過10筆的 user)，和新聞相應的產業類別

code:
透過 final_final 將資料整理成欄位為 user 和其偏好的產業。只有該 user 過去觀看紀錄，有五篇以上新聞有包含該類別的產業，或該類別的產業占其觀看新聞總數的3成以上才將其視為對該產業有興趣，輸出檔案為 user_based_data



notebook: K_means
data:
user_based_data

code:
透過 user_based_data 進行 user 的分群，方法是利用歐基理德距離的 k-means




notebook: process
data:
kmeans_data: 利用 user 的產業偏好做分群，欄位為 user_id 和 group
kmeans_group_num: 不同 group 的組員數量，欄位為 group 和 group_num
final_final: user 過去點擊新聞資料(只包含歷史點擊記錄超過10筆的 user)
df_news_all: 所有新聞的資訊，欄位有 path(資料路徑)、data(新聞發布日期)、news_guid(新聞ID)、title(標題)、news_content(新聞內容)、content_remove_html_tag(去除 html tag 的新聞內容)

code:
利用 knn 求出與某 user 相似的 users ，再找出相似 users 有看過但該 user 沒看過的新聞，並設定這些新聞要被 n 個相似 user 看過，且推薦時間點以前發布的新聞才將其視為要推薦的候選新聞 
透過之前 kmeans 分群的結果找出該 user 是第幾群(k)，再將第k群的 user 有點過的新聞當作正樣本，沒點過的新聞當作負樣本，再將結果當作label，併入df_news_all當中，輸出檔案為 bert_train_data



notebook: train
data:
bert_train_data: 欄位有 path(資料路徑)、data(新聞發布日期)、news_guid(新聞ID)、title(標題)、news_content(新聞內容)、content_remove_html_tag(去除 html tag 的新聞內容)、label(有點: 1 、 未點: 0)

code:
將 bert_train_data 放入 bert 去 train，這樣的方法可以避免在訓練時正負樣本差異太大導致 overfiting 的情況(若將單一 user 點過的資料當作正樣本，正負樣本會過於懸殊)，故退而求其次去計算該 user 的 group 整體的偏好，來代表該user 點擊新聞的偏好





notebook: predict
data:
user_based_recommendation: 透過 notebook: process 當中 KNN model 所推薦的新聞
validation_test_data: 最一開始就先切出來的最後兩筆點擊紀錄(我們這邊指拿其中的第一筆作預測，第二筆沒用到)
df_news_all: 所有新聞的資訊，欄位有 path(資料路徑)、data(新聞發布日期)、news_guid(新聞ID)、title(標題)、news_content(新聞內容)、content_remove_html_tag(去除 html tag 的新聞內容)
target_user_id: 要推薦的 user_id

code:
bert testing
輸出結果為候選新聞容不容易被該 group 的 user 點擊(每一列為不被點擊的機率和被點擊的機率)，檔案為 bert_predict_output




notebook: final_CF
data:
target_user_id: 要推薦的 user_id
validation_test_data: 最一開始就先切出來的最後兩筆點擊紀錄(我們這邊指拿其中的第一筆作預測，第二筆沒用到)
final_final: user 過去點擊新聞資料(只包含歷史點擊記錄超過10筆的 user)
df_news_all: 所有新聞的資訊，欄位有 path(資料路徑)、data(新聞發布日期)、news_guid(新聞ID)、title(標題)、news_content(新聞內容)、content_remove_html_tag(去除 html tag 的新聞內容)
bert_predict_output= 欄位為推薦新聞不被點擊機率和點擊機率
bert_test_data_with_target: 要推薦的候選新聞

code:
找出 Bert 最後一個 layer 的值當作新聞向量(也可以選擇多個 layer 再做平均)
進行推薦候選新聞內容和標題的斷字處理
透過 average word vec 、 doc2vec 、 bag of words 、 TF-IDF 的方法找出代表新聞的向量
再透過這些向量對該 user 最後一篇看過的新聞，做 cosine_similarity 的計算，並透過相似性的結果排名
最終透過類似於 hit-rate 的方式給予分數
