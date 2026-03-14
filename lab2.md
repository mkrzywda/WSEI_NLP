# Laboratorium 2

## Temat

Eksperymenty klasyfikacji tekstu dla całych zbiorów danych z użyciem różnych metod reprezentacji tekstu, klasyfikatorów oraz wizualizacji przestrzeni wektorowej.

Laboratorium rozszerza funkcjonalność bota z **Laboratorium 1**, który obsługiwał klasyfikację pojedynczych wiadomości .

---

# Cel laboratorium

Celem zadania jest rozbudowanie bota Telegram o możliwość:

* trenowania klasyfikatorów na **całych datasetach**
* testowania różnych **embeddingów tekstu**
* uruchamiania wielu **modeli klasyfikacji**
* wykonywania **Grid Search**
* wykonywania wielu uruchomień eksperymentu z różnymi seedami
* generowania **wizualizacji embeddingów**
* zapisywania wyników klasyfikacji i wykresów do plików

---

# Nowa komenda bota

Bot powinien obsługiwać polecenie:

```
/classify dataset=<dataset_name> method=<model> gridsearch=<true/false> run=<n>
```

---

# Przykłady użycia

```
/classify dataset=20news_group method=all gridsearch=false run=1
```

```
/classify dataset=amazon method=logreg gridsearch=true run=2
```

```
/classify dataset=imdb method=rf,nb gridsearch=false run=3
```

```
/classify dataset=ag_news method=rf,nb gridsearch=false run=3
```

---

# Parametry komendy

## dataset (przykładowe)

Określa zbiór danych użyty w eksperymencie.

Możliwe wartości (20news_group):

```
- 20news_group
- https://www.kaggle.com/datasets/lakshmi25npathi/imdb-dataset-of-50k-movie-reviews
- https://www.kaggle.com/datasets/kritanjalijain/amazon-reviewstwitter_sentiment
- ag_news
```

Opis:

| dataset           | opis                                          |
| ----------------- | --------------------------------------------- |
| 20news_group      | dataset `sklearn.datasets.fetch_20newsgroups` |
| imdb              | dataset recenzji filmów                       |
| twitter_sentiment | dataset tweetów z sentimentem                 |


---
# method

Określa model klasyfikacji.

Możliwe wartości:

```
nb
rf
mlp
logreg
all
```

Opis:

| skrót  | model                      |
| ------ | -------------------------- |
| nb     | Multinomial Naive Bayes    |
| rf     | Random Forest              |
| mlp    | MLPClassifier              |
| logreg | LogisticRegression         |
| all    | uruchamia wszystkie modele |

---

# gridsearch

Parametr określa czy należy uruchomić **GridSearchCV**.

Możliwe wartości:

```
gridsearch=true
gridsearch=false
```

### gridsearch=true

Uruchamiane jest strojenie hiperparametrów.

Przykładowe siatki:

### Naive Bayes

```
alpha = [0.1, 0.5, 1.0]
```

### Random Forest

```
n_estimators = [100, 300]
max_depth = [None, 10, 20]
```

### Logistic Regression

```
C = [0.1, 1, 10]
```

### MLP

```
hidden_layer_sizes = [(128,), (256,128)]
```

---

# run

Określa liczbę uruchomień eksperymentu.

Możliwe wartości:

```
run=1
run=2
run=3
```

Każde uruchomienie powinno mieć inny **seed**.

Przykład:

```
seed = 42
seed = 1337
seed = 2024
```

Wyniki należy uśrednić.

---

# Metody reprezentacji tekstu

Eksperyment powinien wykorzystywać następujące embeddingi lub metody reprezentacji tekstu:

```
bow
tfidf
word2vec
glove
```

Opis:

| embedding | opis                            |
| --------- | ------------------------------- |
| bow       | Bag of Words                    |
| tfidf     | TF-IDF                          |
| word2vec  | embedding trenowany na korpusie |
| glove     | pretrenowany embedding          |

---

# Word Cloud

Bot powinien wygenerować word cloud:

### dla całego korpusu

```
lab2plots/wordcloud_corpus.png
```

### dla każdej klasy

```
lab2plots/wordcloud_class_<class>.png
```

---

# Wizualizacja embeddingów

Należy zastosować metody redukcji wymiarowości:

```
PCA
t-SNE
TruncatedSVD
```

Wizualizacje zapisać do plików:

```
lab2plots/{dataset}_{modelname}_{text_reprezentation_method}_pca_embedding.png
lab2plots/{dataset}_{modelname}_{text_reprezentation_method}tsne_embedding.png
lab2plots/{dataset}_{modelname}_{text_reprezentation_method}svd_embedding.png
```

Wizualizacja powinna przedstawiać:

```
punkt = dokument
kolor = klasa
```

---

# Feature importance

Należy zapisać do pliku feature importance (top5 albo top10) dla danej klasy w danym zbiorze danych.


# Confusion Matrix

Dla każdego modelu należy wygenerować **macierz pomyłek**.

Pliki zapisać jako:

```
lab2plots/confusion_<embedding>_<model>.png
```

Przykłady:

```
lab2plots/confusion_tfidf_logreg.png
lab2plots/confusion_bow_nb.png
lab2plots/confusion_word2vec_rf.png
```

Macierz powinna przedstawiać:

* rzeczywiste klasy
* przewidziane klasy
* liczebność predykcji.

---

# Podobne słowa (embeddingi)

Jeżeli używany jest:

```
word2vec
```

lub

```
glove
```

bot powinien wygenerować podobne słowa dla przykładowych zapytań np. dla datasetu 20news:

```
space
computer
science
music
car
```

Wyniki zapisać do pliku:

```
lab2_similar_words.txt
```


---

# Wizualizacja słów w embeddingu

Dla wybranych słów należy wygenerować wizualizację embeddingów przy użyciu:

```
PCA
t-SNE
```

Zapisać do plików:

```
lab2plots/word_embedding_pca.png
lab2plots/word_embedding_tsne.png
```

---

# Wyniki eksperymentów

Wyniki klasyfikacji należy zapisać do pliku:

```
lab2results.csv
```

Plik powinien zawierać:

```
embedding
model
accuracy
macro_f1
seed
```


---