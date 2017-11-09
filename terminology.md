# 名詞解釋

以下名詞會不斷地在我們對 effective machine learning 的討論中出現：

以下是我認為使用原文會比較精準的詞彙，就不做另外翻譯：

*Heuristic:*  先验办法，往往是人凭着经验给出来的。最简单的例子就是挑西瓜：不需要机器学习我也知道，个大，敲起来声音透亮，色泽鲜艳的西瓜比较甜。

*Infrastructure:* 基礎建設。那些建造一個機器學習系統所需要的任何軟硬體，包括資料流、資料庫、機器、網路、軟體服務等等。

以下是原文的名詞解釋：

*Instance:* 你想要預測的事物。舉例來說，Instance 可能是一個網頁，你想要把它「分類」為「與貓有關」或「與貓無關」。

*Label:* 預測的答案 -- 可以是機器學習系統產生的答案，也可以是 training data 提供的正確答案。舉例來說，一個網頁的 label 可能是「與貓有關」。

*Feature:* instance在預測任務中的其中一個屬性。舉例來說，一個網頁可能會有一個「包含『貓』這個字」的 feature。

*Feature Column:* 一組相關的 feature 集合。像是一個使用者可能居住的所有可能國家。一個 example 可能會有一個會多個 feature 出現在 feature column。一個 feature column 在 Yahoo/Microsoft 的 VW \(Vowpal Wabbit\) 系統被稱為命名空間（namespace），或場（field）。
> 按：Google 特有名詞

*Example:*  样本。一個有 label 以及它自己 feature 的 instance。

*Model:*  模型。對預測的統計表示（representation）。你使用 examples 訓練模型、然後用模型作預測。

*Metric:*  指标。一个你在意的数据或数字，不管以后在特征中是否会用的到。

*Objective:*  优化目标。一個你的演算法試著去優化的 metric。

*Pipeline:* 泛指圍繞在 ML 演算法的基礎建設（infrastructure）。包含從前端收集資料、將它們放到 training data file 裏、訓練一個或多個 model，以及將 model 輸出到 production 環境上。
> 按：可以是指資料流，廣義來說也可以指資料流經過的整個系統，就像食品的製造鏈一樣
