# 文本相似度
- 判断相似角度：语义角度、字面角度
- 方法：
    * 余弦相似度、向量空间模型
    * TFIDF
    * LCS
## 余弦相似度 - 计算个体间的相似程度
中文分词 -> 列出所有词（词包，词库） -> 计算词频 -> 词频向量化 -> 套公式计算
### 处理流程：
1。找出两篇文章的关键词。
2。每篇文章取若干词，合并成一个集合，计算每篇文章对于集合中的词的词频。
3。生成两篇文章各自的词频向量。
4。计算两个向量的余弦相似度，值越大表示越相似。


