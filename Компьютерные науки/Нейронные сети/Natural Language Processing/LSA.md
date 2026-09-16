---
jupyter:
  jupytext:
    cell_metadata_filter: -all
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.19.5
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

**LSA** — это метод понижения размерности для текстовых данных. Он позволяет найти **скрытые темы** в корпусе документов.

LSA применяет **SVD** (сингулярное разложение) к матрице [[TF-IDF]] и обрезает результат, оставляя только `k` главных компонент:

```python
from sklearn.feature_extraction.text import TfidfVectorizer  
from sklearn.decomposition import TruncatedSVD  
import pandas as pd  
  
texts = [  
    "Я люблю кошек",  
    "Я люблю собак",  
    "Я ненавижу кошек",  
    "Я ненавижу собак",  
]  
  
vectorizer = TfidfVectorizer()  
  
tf_idf_matrix = vectorizer.fit_transform(texts)  
  
td_idf_df = pd.DataFrame(  
    tf_idf_matrix.toarray(),  
    columns=[f"{theme}" for theme in vectorizer.get_feature_names_out()],  
    index=[f"Text {i+1}" for i in range(len(texts))]  
)  
  
print(td_idf_df)  
  
svd = TruncatedSVD(n_components=2)  
  
svd_matrix = svd.fit_transform(tf_idf_matrix)  
  
svd_df = pd.DataFrame(  
    svd_matrix,  
    columns=[f"Скрытая тема {i+1}" for i in range(len(svd.get_feature_names_out()))],  
    index=[f"Text {i+1}" for i in range(len(texts))]  
)  
  
print(svd_df)
```
