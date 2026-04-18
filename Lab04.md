# Laboratorium 4

## Temat

Ekstrakcja informacji: Named Entity Recognition (NER), Named Entity Linking (NEL), Named Entity Disambiguation (NED), tłumaczenie maszynowe oraz podsumowanie tekstu przy użyciu LLM w Ollama.

Laboratorium rozszerza funkcjonalność bota z **Laboratorium 1**, **Laboratorium 2** i **Laboratorium 3** o:

- rozpoznawanie nazwanych entitetów za pomocą `Spacy` i `Stanza`,
- linkowanie i disambiguacja entitetów z bazą wiedzy,
- tłumaczenie maszynowe bez konieczności posiadania klucza API,
- generowanie podsumowań tekstu przy użyciu małych modeli LLM na Ollama,
- integracja wielu zaawansowanych narzędzi NLP w jednym bocie,
- poprawę modularności i organizacji kodu.

---

## Cel laboratorium

Celem zadania jest rozbudowanie istniejącego bota Telegram tak, aby potrafił:

- automatycznie rozpoznawać i wyodrębniać byty (osoby, miejsca, organizacje, itp.),
- linkować rozpoznane byty z bazą wiedzy i wikidanymi,
- disambiguować byty w kontekście dokumentu,
- tłumaczyć teksty między różnymi językami,
- generować streszczenia tekstów za pomocą LLM,
- zapisywać wyniki analizy do plików,
- działać w bardziej złożony i zaawansowany sposób.

---

## Wymagane metody rozpoznawania entitetów (NER)

Bot powinien umożliwiać rozpoznawanie entitetów przy użyciu co najmniej dwóch podejść:

1. **Spacy** - pretrenowany model `pl_core_news_sm` lub `en_core_web_sm`,
2. **Stanza** - model dla wybranego języka.

Przykładowe typy rozpoznawanych encji:

```
PERSON      - osoby
ORG         - organizacje
GPE         - geopolityczne jednostki (kraje, miasta)
PRODUCT     - produkty
LOCATION    - lokalizacje
DATE        - daty
TIME        - czasy
MONEY       - kwoty pieniężne
PERCENT     - wartości procentowe
FACILITY    - obiekty
EVENT       - wydarzenia
```

---

## Named Entity Linking (NEL) i Named Entity Disambiguation (NED)

Bot powinien umożliwiać linkowanie i disambiguację entitetów za pomocą:

1. **Wikidane (Wikidata)** - poprzez API albo lokalną bazę,
2. **Wikipedia** - wyszukiwanie podobnych artykułów,
3. **Lokalna baza wiedzy** - pliki CSV/JSON.

### NEL - zadania do implementacji:

- wyszukiwanie kandydatów dla danego entitetu,
- przypisywanie wymiarów (identyfikatorów Wikidata),
- obsługa wieloznaczności (kilka możliwych interpretacji).

### NED - zadania do implementacji:

- kontekstowe wybieranie właściwej interpretacji,
- ranking kandydatów na podstawie podobieństwa,
- wyltrowanie o niskiej pewności.

---

## Tłumaczenie maszynowe

Bot powinien obsługiwać tłumaczenie maszynowe bez API przy użyciu:

1. **Helsinki-NLP/Opus-MT** (Hugging Face) - wytrenowane modele tłumaczenia albo Opus-MT, mBART50_m2m, M2M_100.
2. **Transformers + PyTorch** - biblioteka `transformers` z modelami `opus-mt`,
3. **Google Translate API** (alternatywa - opcjonalnie),
4. **Libre Translate** (darmowy, host-able).

Obsługiwane pary języków (minimum):

```
en <-> pl
en <-> de
en <-> fr
pl <-> de
pl <-> en
pl <-> fr
```

Można rozszerzyć do więcej kombinacji.

---

## Podsumowanie tekstu z Ollama

Bot powinien generować streszczenia tekstów za pomocą małych modeli LLM dostępnych w Ollama:

### Typ podsumowania:

1. **Extractive** - wyodrębnianie kluczowych zdań,
2. **Abstractive** - stworzenie nowego tekstu streszczającego,
3. **Bullet-point** - streszczenie punktowe,
4. **Custom prompt** - podsumowanie z niestandardowego promptu.

Bot powinien:

- wczytywać model z lokalnej instalacji Ollama,
- generować streszczenia o zmiennej długości (short/medium/long),
- obsługiwać timeout dla długich tekstów,
- zapisywać wygenerowane streszczenia.

---

## Nowe komendy bota

Bot powinien obsługiwać poniższe polecenia:

```text
/ner method=<spacy|stanza> text="tekst"
/nel text="tekst" language=<en|pl>
/ned entity="",<context="tekst>
/translate text="tekst" target_lang=<en|pl|de|fr|es>
/summarize text="tekst" summary_type=<extractive|abstractive|bullets>
/summarize text="tekst" length=<short|medium|long>
/analyze_entities text="tekst" link=<true|false>
/knowledge_graph text="tekst" (opcjonalnie)
/language_detect text="tekst"
/help
```

## Przykłady użycia

### NER

```text
/ner method=spacy text="Steve Jobs, założyciel Apple'a, urodził się w San Francisco."
```

Oczekiwana odpowiedź:

```
Metoda: Spacy
TEXT: Steve Jobs, założyciel Apple'a, urodził się w San Francisco.

ENTITIES:
- Steve Jobs (PERSON) [0:11]
- Apple (ORG) [24:29]
- San Francisco (LOCATION) [50:64]
```

### NEL

```text
/nel text="Steve Jobs" language=en
```

Oczekiwana odpowiedź:

```
Entity: Steve Jobs (PERSON)
Candidates:
1. Steve Jobs (Q19837) - Apple co-founder
   - Wikipedia: https://en.wikipedia.org/wiki/Steve_Jobs
   - Confidence: 0.98
2. Steve Jobs (Q1234567) - Other person named Steve Jobs
   - Confidence: 0.05
```

### Tłumaczenie

```text
/translate text="The quick brown fox jumps over the lazy dog" target_lang=pl
```

Oczekiwana odpowiedź:

```
Source: en
Target: pl
Translation: Szybki brązowy lis przeskakuje leniwego psa
```

### Podsumowanie

```text
/summarize text="Tekst można tutaj umieścić" summary_type=abstractive length=medium
```

Oczekiwana odpowiedź:

```
Model: Bielik
Text length: 542 tokens
Summary type: Abstractive
Summary length: Medium

SUMMARY:
[wygenerowane podsumowanie]

Generation time: 2.34s
```

### Analiza połączona

```text
/analyze_entities text="Elon Musk posiada firmę Tesla, oraz xAI w Austin." link=true
```

Oczekiwana odpowiedź:

```
ENTITIES FOUND:
- Elon Musk (PERSON) [0:10]
  Wikidata: Q317521
  Wikipedia: https://en.wikipedia.org/wiki/Elon_Musk

- Tesla (ORG) [21:26]
  Wikidata: Q478214
  Wikipedia: https://en.wikipedia.org/wiki/Tesla,_Inc.

- xAI (ORG) [30:33]
  Wikidata: Not found
  
- Austin (GPE) [40:45]
  Wikidata: Q16563
  Wikipedia: https://en.wikipedia.org/wiki/Austin,_Texas

KNOWLEDGE GRAPH:
Elon Musk --founder--> Tesla
Elon Musk --founded--> xAI
xAI --located-in--> Austin
```

---

## Graf wiedzy (Knowledge Graph) - Opcjonalnie.

Bot powinien generować wizualne reprezentacje relacji między entitetami:

- tworzenie grafu na podstawie wyodrębnionych entitetów,
- pokazywanie relacji między entitetami,
- wizualizacja za pomocą `networkx` + `matplotlib`,
- zapis do pliku PNG.

Przykładowa struktura grafu:

```
Nodes: PERSON, ORG, LOCATION, DATE
Edges: founded, worked_for, located_in, belongs_to
```

Plik do zapisania:

```text
lab4plots/knowledge_graph_<timestamp>.png
```

---

## Detekcja języka

Bot powinien automatycznie:

- rozpoznawać język tekstu, np. przy `/translate` i `/summarize`,
- obsługiwać co najmniej 4 języków,
- ułatwiać międzyjęzyczną analizę tekstu.

Biblioteka rekomendowana: `langdetect` lub `textblob`.

---

## Wymagania dla modeli

### Spacy

```python
import spacy
nlp = spacy.load("en_core_web_sm") 
```

Wymaga instalacji modelu:

```bash
python -m spacy download en_core_web_sm
python -m spacy download pl_core_news_sm
```

### Stanza

```python
import stanza
nlp = stanza.Pipeline(lang='en', processors='tokenize,ner')
```

Automatycznie pobiera modele.

### transformers + Helsinki-NLP/Opus-MT

```bash
pip install transformers torch
```

Modele będą automatycznie pobierane z Hugging Face przy pierwszym uruchomieniu.

### Ollama

Wymaga zainstalowanego Ollama na maszynie:

```bash
# Na macOS
brew install ollama

# Na Linuxie
curl -fsSL https://ollama.ai/install.sh | sh

# Pobranie modelu
ollama pull <model_name>
```

Komunikacja z Ollama poprzez lokalne API:

```python
import requests
response = requests.post('http://localhost:11434/api/generate', json={...})
```