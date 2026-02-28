# Laboratorium 1

## Temat

Prosty bot Telegram do przetwarzania i klasyfikacji pojedynczych wiadomości tekstowych.

## Cel laboratorium

Celem zadania jest przygotowanie bota Telegram w Pythonie, który obsługuje trzy tryby pracy:

```text
/task <nazwa_zadania> "tekst" "klasa"
/full_pipeline "tekst" "klasa"
/classifier "tekst"
/stats
```

Bot ma umożliwiać:

- wykonanie pojedynczego zadania NLP na wiadomości,
- wykonanie pełnego pipeline'u przetwarzania tekstu,
- budowanie zbioru danych na podstawie wiadomości oznaczanych przez użytkownika,
- klasyfikację nowej wiadomości na podstawie danych zapisanych w pliku `JSON`,
- generowanie wykresów dla wybranych operacji.

## Założenia dotyczące danych

Po każdym wywołaniu polecenia `/task` lub `/full_pipeline` należy dopisać tekst i jego klasę do pliku `JSON`, na przykład `sentences.json`, który będzie budowany w trakcie pracy z botem.

Plik ze zdaniami ma zawierać wyłącznie rekordy w postaci:

```json
[
  { "text": "kocham ten film", "class": "pozytywny" },
  { "text": "to był okropny film", "class": "negatywny" },
  { "text": "to był zwykły dzień", "class": "neutralny" }
]
```

Każdy rekord ma zawierać tylko dwa pola:

- `text` - treść wiadomości,
- `class` - przypisana klasa.

Do tego pliku nie należy dodawać innych pól.

## Wymagane tryby pracy

### 1. Tryb `/task`

Tryb `/task` służy do wykonania jednego, wskazanego zadania NLP oraz do zapisania oznaczonego przykładu w pliku `JSON`.

Składnia:

```text
/task tokenize "To był bardzo interesujący wykład." "neutralny"
```

Pierwszy argument po komendzie określa nazwę zadania, na przykład:

- `tokenize`,
- `remove_stopwords`,
- `lemmatize`,
- `stemming`,
- `stats`,
- `n-grams`,
- `plot_histogram`,
- `plot_wordcloud`.

W tym trybie bot powinien:

1. wykonać wskazaną operację na podanym tekście,
2. zwrócić wynik tej operacji użytkownikowi,
3. dopisać do pliku `sentences.json` rekord,
4. jeśli wybrane zadanie dotyczy wizualizacji, wygenerować wykres i zapisać go do pliku.

Nazwy plików z wykresami powinny mieć postać:

```text
Sentence_{YYYY-MM-DD}_{HH-MM-SS}.png
```

Przykładowe wykresy w trybie `/task`:

- histogram długości tokenów,
- word cloud dla najczęstszych słów,
- wykres słupkowy najczęstszych tokenów.

```json
{ "text": "To był bardzo interesujący wykład.", "class": "neutralny" }
```

### 2. Tryb `/full_pipeline`

Tryb `/full_pipeline` służy do uruchomienia pełnego pipeline'u przetwarzania tekstu oraz do zapisania oznaczonego przykładu w pliku `JSON`. 

Składnia:

```text
/full_pipeline "System działa szybko, ale interfejs wymaga poprawy." "neutralny"
```

W tym trybie bot powinien wykonać kolejno:

1. czyszczenie tekstu,
2. tokenizację,
3. usunięcie stop words,
4. lematyzację,
5. stemming,
6. reprezentację tekstu `Bag of Words`,
7. reprezentację tekstu  `TF-IDF`,
8. wyznaczenie statystyk dotyczących wprowadzonego tekstu,
9. wygenerowanie wykresów,
10. prezentację wyników użytkownikowi,
11. dopisanie do pliku `sentences.json` rekordu:

```json
{ "text": "System działa szybko, ale interfejs wymaga poprawy.", "class": "neutralny" }
```

W trybie `/full_pipeline` należy wygenerować co najmniej:

- wykres słupkowy najczęstszych słów (w zdaniu),
- histogram długości tokenów,
- word cloud.

Wygenerowane wykresy należy zapisać do plików `.png` i (jeżeli to możliwe) przesłać również użytkownikowi przez bota.

Jeżeli wprowadzony tekst składa się z więcej niż jednego zdania, należy podzielić go na zdania i każdemu zdaniu naiwnie przypisać tę samą klasę.

### 3. Tryb `/classifier`

Tryb `/classifier` służy do klasyfikacji nowej wiadomości na podstawie danych zapisanych wcześniej w pliku `sentences.json`.

Składnia:

```text
/classifier "To był fantastyczny film"
```

W tym trybie bot powinien:

1. wczytać dane z pliku `sentences.json`,
2. przygotować dane do uczenia modelu,
3. zbudować prosty klasyfikator tekstu,
4. przewidzieć klasę dla nowej wiadomości,
5. zwrócić użytkownikowi przewidzianą etykietę.

Minimalny zestaw klas:

- `pozytywny` - wartość liczbowa `1`,
- `neutralny` - wartość liczbowa `0`,
- `negatywny` - wartość liczbowa `-1`.

## Przykład klasyfikatora

Przykładowa implementacja klasyfikatora może wyglądać następująco:

```python
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline

# Dane treningowe
texts = [
    "kocham ten film",
    "to był okropny film",
    "fantastyczna historia",
    "nienawidzę tego"
]

labels = [1, 0, 1, 0]  # 1 = pozytywny, 0 = negatywny

# Pipeline
model = Pipeline([
    ("vectorizer", CountVectorizer()),
    ("classifier", LogisticRegression())
])

model.fit(texts, labels)

# Predykcja
print(model.predict(["to był fantastyczny film"]))
```

W rozwiązaniu docelowym dane do uczenia powinny zostać wczytane z pliku `sentences.json`, a nie wpisane na stałe w kodzie.

Opcjonalnie można przetestować /classifier with_preprocesing/without_preprocessing - czyli tekst powinien zostać wcześnie przeprocesowany wybranymi narzędziami.

### 4. Tryb `/classifier`

Tryb `/stats` służy do wyswietlenie unikalnych tokenów, unikalnych n-gramów (2-gram, 3-gram) na podstawie danych zapisanych wcześniej w pliku `sentences.json` i przedstawienie wizualizaji:
  - wykres słupkowy najczęstszych słów (w całym zbiorze),
  - histogram długości tokenów,
  - word cloud.
  - liczność klas poszczególnych tekstów

Składnia:

```text
/stats
```


## Wymagania funkcjonalne

Bot powinien:

- poprawnie obsługiwać trzy komendy: `/task`, `/full_pipeline`, `/classifier`,
- umożliwiać przekazanie klasy przy komendach `/task` i `/full_pipeline`,
- zapisywać nowe przykłady do pliku `JSON` w poprawnym formacie,
- wykorzystywać zapisany zbiór danych podczas działania `/classifier`,
- zwracać odpowiedź w czytelnej formie tekstowej,
- generować i zapisywać wykresy dla operacji wizualizacyjnych.

## Wymagania techniczne

Rozwiązanie powinno spełniać następujące warunki:

- język programowania: Python,
- komunikacja przez Telegram Bot API,
- użycie biblioteki `nltk`
- użycie przynajmniej jednego prostego modelu klasyfikacyjnego z `scikit-learn`,
- użycie biblioteki do tworzenia wykresów, na przykład `matplotlib`, `seaborn` lub `wordcloud`,
- czytelny podział kodu na funkcje lub moduły,
- obsługa błędów dla pustej wiadomości, niepoprawnej komendy lub braku danych w pliku `JSON`,
- możliwość uruchomienia projektu na podstawie krótkiej instrukcji w `README`.

## Minimalny zakres oceny

Podczas oceny będą brane pod uwagę:

- poprawna obsługa komendy `/task <nazwa_zadania> "tekst" "klasa"`,
- poprawna obsługa komendy `/full_pipeline "tekst" "klasa"`,
- poprawna obsługa komendy `/classifier "tekst"`,
- poprawność formatu pliku `sentences.json`,
- poprawność działania analizy tekstu,
- poprawność działania klasyfikatora,
- poprawność generowania i zapisywania wykresów,
- czytelność odpowiedzi zwracanej użytkownikowi,
- jakość kodu i organizacja projektu.

## Co należy oddać

- kod źródłowy projektu (plik/pliki python),
- plik `README` z instrukcją uruchomienia,
- przykładowy plik `sentences.json`,
- przykładowe wygenerowane wykresy (w katalogu plots/),
- krótkie przykłady użycia wszystkich trzech trybów
