---
aliases:
  - Term frequency
  - Inverse Document Frequency
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
Это статистическая мера, которая показывает важность слова в рамках конкретного документа из набора документов.
#### Term frequency
Частота терма в документе. 
Если `терм` встречается часто в **одном конкретном документе**, то этот `терм` важно для этого текста.

Считается как:
$$
\frac{\text{Количество раз, которое терм встречается в документе}}{\text{Количество терм в документе}}
$$

---

#### Inverse document frequency
Показывает на сколько редко терм встречается во всех документах в целом.

Считается как:
$$
\log\frac{\text{Количество документов}}{\text{Количество документов, содержащих этот терм}}
$$
---

#### TF-IDF Формула
$$
\text{TF-IDF} = TF \cdot IDF
$$
Перемножая, мы получаем величину, где:
- TF говорит, что терм важен для этого документа
- IDF говорит, что терм редко встречается в других документах

---

Применяется в:
- поисковых системах
- классификации текстов
- поиске похожих документов

---

Главный минус - алгоритм не учитывает **позиционирование** термов в документах.

---

```python
import pandas as pd  
from sklearn.feature_extraction.text import TfidfVectorizer  
  
texts = [  
    "Я люблю кошек",  
    "Я люблю собак",  
    "Я ненавижу кошек"  
]  
  
  
vectorizer = TfidfVectorizer()  
  
result = vectorizer.fit_transform(texts)  
  
df = pd.DataFrame(  
    result.toarray(),  
    columns=vectorizer.get_feature_names_out(),  
    index=[f"Text {i+1}" for i in range(len(texts))]  
)  
  
print(df)
```
