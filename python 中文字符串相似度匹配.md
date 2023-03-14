# python 中文字符串相似度匹配

## 想法
中文字符串相似度匹配

## 摘录内容 
以下是一个使用 FuzzyWuzzy 和 jieba 库计算两个中文字符串的 Jaccard 相似度的示例代码：
```
import jieba
from fuzzywuzzy import fuzz

def split_text_to_words(text):
    return [word for word in jieba.cut(text)]

company1 = "苹果公司"
company2 = "苹果公司（中国）有限公司"

words1 = split_text_to_words(company1)
words2 = split_text_to_words(company2)

similarity = fuzz.token_set_ratio(" ".join(words1), " ".join(words2))
print("Jaccard similarity:", similarity)
```

在上面的代码中，split_text_to_words 函数使用 jieba 库对中文字符串进行分词，并将分词后的字符串拼接成一个字符串，再使用 FuzzyWuzzy 库计算 Jaccard 相似度。

```
pip install jieba fuzzywuzzy
```