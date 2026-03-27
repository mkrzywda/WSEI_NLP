# Laboratorium 3

## Temat

Analiza sentymentu oraz sekwencyjne metody klasyfikacji tekstu w bocie Telegram.

Laboratorium rozszerza funkcjonalność bota z **Laboratorium 1** i **Laboratorium 2** o:

- analizę sentymentu pojedynczych tekstów,
- porównanie wielu podejść do klasyfikacji sentymentu,
- trenowanie modeli sekwencyjnych `SimpleRNN`, `LSTM` i `GRU`,
- zapisywanie i ponowne wczytywanie wytrenowanych modeli z plików `.h5`,
- poprawę czytelności, organizacji i użyteczności całego chatbota.

---

## Cel laboratorium

Celem zadania jest rozbudowanie istniejącego bota Telegram tak, aby potrafił:

- wykonać analizę sentymentu dla pojedynczej wiadomości,
- trenować modele na wybranym zbiorze danych,
- porównać jakość różnych metod klasyfikacji tekstu,
- zapisać wyniki eksperymentów, wykresy i modele do plików,
- działać w bardziej czytelny, modularny i przyjazny dla użytkownika sposób.

---

## Zakres rozszerzenia względem poprzednich laboratoriów

W Laboratorium 3 bot powinien zachować wcześniejsze funkcjonalności, a dodatkowo obsługiwać:

- analizę sentymentu dla jednej wiadomości,
- trenowanie modeli sekwencyjnych na datasetach,
- wczytywanie wcześniej zapisanych modeli neuronowych,
- porównanie wyników kilku metod dla tego samego datasetu,
- zarządzanie własnym zbiorem danych `sentiment_dataset.csv`.

---

## Wymagane metody analizy sentymentu

Bot powinien umożliwiać klasyfikację tekstu przy użyciu co najmniej następujących podejść:

1. system rule-based do analizy sentymentu,
2. metoda ML-based: `Naive Bayes` albo `Random Forest`,
3. metoda oparta o transformer,
4. metoda oparta o `TextBlob`,
5. metoda oparta o `Stanza`,
6. metoda oparta o sieć `SimpleRNN`,
7. metoda oparta o sieć `LSTM`,
8. metoda oparta o sieć `GRU`.

Dla modeli neuronowych:

- model ma być wcześniej wytrenowany,
- model ma być zapisany do pliku `.h5`,
- podczas predykcji bot ma wczytywać model z pliku zamiast trenować go od nowa.

---

## Zbiory danych

Do eksperymentów można wykorzystać jeden z trzech wariantów:

- `amazon` - dataset recenzji produktów,
- `imdb` - dataset recenzji filmowych,
- `custom` - własny dataset zapisany w pliku `sentiment_dataset.csv`. (dodaj własne przykłady, dodaj funkcjonalność w chacie dodawania danych do datasetu)

### Własny dataset

Jeżeli używany jest dataset własny, bot powinien dopisywać nowe przykłady do pliku:

```text
sentiment_dataset.csv
```

Minimalny format pliku:

```csv
text,label
"Uwielbiam ten film",pozytywny
"To był zwykły dzień",neutralny
"Ten produkt jest fatalny",negatywny
```

Minimalny zestaw klas:

- `pozytywny`,
- `neutralny`,
- `negatywny`.

(możesz rozszerzyć)

---

## Nowe komendy bota

Bot powinien obsługiwać poniższe polecenia:

```text
/sentiment method=<metoda> text="tekst"
/train model=<simplernn|lstm|gru> dataset=<amazon|imdb|custom>
/compare dataset=<amazon|imdb|custom> methods=<lista_metod>
/add_sentiment "tekst" "etykieta"
/models
/help
```

Bot może dodatkowo zachować komendy z poprzednich laboratoriów, jeżeli są nadal przydatne:

- `/task`
- `/full_pipeline`
- `/classifier`
- `/classify`
- `/stats`

---

## Przykłady użycia

```text
/sentiment method=rule text="To był naprawdę świetny film"
```

```text
/sentiment method=transformer text="Produkt przyszedł uszkodzony i jestem bardzo rozczarowany"
```

```text
/train model=lstm dataset=imdb
```

```text
/train model=simplernn dataset=amazon
```

```text
/train model=gru dataset=custom
```

```text
/compare dataset=custom methods=rule,nb,transformer,textblob,stanza,simplernn,lstm,gru
```

```text
/add_sentiment "Obsługa była poprawna, ale niczym mnie nie zachwyciła" "neutralny"
```

---

## Wymagania dla modeli sekwencyjnych

### 1. SimpleRNN

Model powinien zawierać przynajmniej:

- warstwę `Embedding`,
- warstwę `SimpleRNN`,
- warstwę gęstą `Dense`,
- warstwę wyjściową dopasowaną do liczby klas.

### 2. LSTM

Model powinien zawierać przynajmniej:

- warstwę `Embedding`,
- warstwę `LSTM`,
- warstwę `Dense`,
- warstwę wyjściową dopasowaną do liczby klas.

### 3. GRU

Model powinien zawierać przynajmniej:

- warstwę `Embedding`,
- warstwę `GRU`,
- warstwę `Dense`,
- warstwę wyjściową dopasowaną do liczby klas.

### Parametry przykładowe

Można użyć przykładowych parametrów:

```text
embedding_dim = 100
max_len = 200
batch_size = 32
epochs = 5-15
early_stopping=10% epochs.
```

Wartości mogą zostać dobrane inaczej, o ile zostaną opisane w `README`.
Zaproponuj potencjalne eksperymentowanie tzn. max_len z przedziału <x,y> i uruchamiać model dla różnych rozmiarów. 


---

## Pliki modeli

Wytrenowane modele powinny być zapisywane w katalogu:

```text
models/
```

Przykładowe nazwy:

```text
models/simplernn_imdb.h5
models/lstm_imdb.h5
models/gru_imdb.h5
models/simplernn_amazon.h5
models/lstm_custom.h5
models/gru_custom.h5
```

Dodatkowo należy zapisać obiekty potrzebne do późniejszej predykcji:

```text
models/simplernn_imdb_tokenizer.h5
models/simplernn_imdb_label_encoder.h5
models/lstm_imdb_tokenizer.h5
models/gru_custom_label_encoder.h5
```

---

## Tryb `/sentiment`

Komenda:

```text
/sentiment method=<metoda> text="tekst"
```

Powinna:

1. przyjąć tekst od użytkownika,
2. uruchomić wybraną metodę klasyfikacji,
3. zwrócić etykietę sentymentu,
4. zwrócić ocenę lub prawdopodobieństwo, jeżeli jest dostępne,
5. w czytelnej formie pokazać, jaki model został użyty.

Możliwe wartości `method`:

```text
rule
nb
rf
transformer
textblob
stanza
simplernn
lstm
gru
```

---

## Tryb `/train`

Komenda:

```text
/train model=<simplernn|lstm|gru> dataset=<amazon|imdb|custom>
```

Powinna:

1. uruchomić trening wskazanego modelu,
2. zapisać model do pliku `.h5`,
3. zapisać tokenizer i encoder etykiet,
4. zwrócić użytkownikowi podsumowanie treningu,
5. zwrócić ścieżki do zapisanych plików,
6. wygenerować wykres `accuracy/loss`.

Przykładowe nazwy plików wykresów:

```text
lab3plots/train_history_simplernn_imdb.png
lab3plots/train_history_lstm_amazon.png
lab3plots/train_history_gru_custom.png
```

---

## Tryb `/compare`

Komenda:

```text
/compare dataset=<amazon|imdb|custom> methods=<lista_metod>
```

Powinna:

1. uruchomić wskazane metody na tym samym zbiorze danych,
2. porównać ich wyniki,
3. zapisać wyniki do pliku `.csv`,
4. wygenerować tabelę lub wykres porównawczy.

Przykładowy plik z wynikami:

```text
lab3results.csv
```

Plik powinien zawierać co najmniej kolumny:

```text
dataset
method
accuracy
precision
recall
macro_f1
model_path
```

---

## Tryb `/add_sentiment`

Komenda:

```text
/add_sentiment "tekst" "etykieta"
```

Powinna:

1. dopisać nowy rekord do pliku `sentiment_dataset.csv`,
2. sprawdzić poprawność etykiety,
3. zwrócić użytkownikowi potwierdzenie zapisu.

Jeżeli tekst jest wielozdaniowy, można:

- potraktować go jako jeden rekord,
- albo podzielić na zdania i zapisać każde zdanie osobno z tą samą etykietą.

Wybrane rozwiązanie należy opisać w `README`.

---

## Tryb `/models`

Komenda:

```text
/models
```

Powinna wyświetlać:

- listę dostępnych modeli zapisanych w katalogu `models/`,
- informację, dla jakiego datasetu zostały wytrenowane,
- informację, czy dostępny jest tokenizer i encoder etykiet.

---

## Wyniki i wizualizacje

Należy wygenerować i zapisać:

- macierze pomyłek dla wybranych metod,
- wykresy historii uczenia dla `SimpleRNN`, `LSTM`, `GRU`,
- wykres porównujący metody,
- word cloud dla klas sentymentu,
- opcjonalnie wykres rozkładu klas w `sentiment_dataset.csv`.

Przykładowe nazwy plików:

```text
lab3plots/confusion_lstm_imdb.png
lab3plots/confusion_gru_custom.png
lab3plots/compare_methods_imdb.png
lab3plots/wordcloud_pozytywny.png
lab3plots/class_distribution_custom.png
```

---

## Poprawa funkcjonalności i czytelności chatbota

W tym laboratorium należy poprawić jakość całego projektu, nie tylko dodać nowe modele.

### Wymagania dotyczące architektury

Kod powinien zostać podzielony na logiczne moduły, na przykład:

```text
bot.py
commands.py
datasets.py
preprocessing.py
sentiment_methods.py
training.py
model_loader.py
visualizations.py
utils.py
config.py
```

Nie należy umieszczać całej logiki w jednym pliku.

### Wymagania dotyczące czytelności

Projekt powinien zawierać:

- spójne nazewnictwo funkcji i plików,
- krótkie i zrozumiałe funkcje,
- wydzielone stałe i konfigurację,
- czytelne komunikaty zwracane użytkownikowi,
- walidację parametrów wejściowych,
- obsługę błędów, na przykład dla braku modelu lub pustego datasetu,
- komendę `/help` z przykładami użycia.

### Wymagania dotyczące UX bota

Bot powinien:

- informować użytkownika, że rozpoczął trening,
- informować o zakończeniu treningu,
- podawać czas działania lub liczbę epok, jeżeli to możliwe,
- zwracać błędy w przyjaznej formie,
- wyjaśniać, gdy model `.h5` nie istnieje i trzeba go najpierw wytrenować.

Przykład czytelnej odpowiedzi:

```text
Model: LSTM
Dataset: imdb
Predykcja: pozytywny
Pewność: 0.91
```

---

## Wymagania techniczne

Rozwiązanie powinno spełniać następujące warunki:

- język programowania: `Python`,
- komunikacja przez `Telegram Bot API`,
- przetwarzanie danych: `pandas`, `numpy`,
- modele klasyczne: `scikit-learn`,
- modele sekwencyjne: `tensorflow` lub `keras`,
- transformer: `transformers`,
- analiza językowa: `TextBlob`, `Stanza`,
- wizualizacje: `matplotlib`, `seaborn`, `wordcloud`,
- zapis i odczyt plików `csv`, `.h5`,
- projekt powinien dać się uruchomić na podstawie krótkiej instrukcji w `README`.

---