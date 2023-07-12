# SUMMARY - ChatGPT Prompt Engineering for Developers


```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
    )
    return response.choices[0].message["content"]
```

## Prompting Principles
- **Principle 1: Pisz jasne i konkretne instrukcje**
- **Principle 2: Daj modelowi czas na „pomyślenie”**

### Principle 1: Napisz jasne i konkretne instrukcje

#### Tactic 1: Użyj ograniczników, aby wyraźnie wskazać odrębne części danych wejściowych
- Delimiters can be anything like: ```, """, < >, `<tag> </tag>`, `:`
  
```python
text = """...."""

prompt = f"""
Podsumuj jednym zdaniem tekst znajdujący pomiędzy znacznikami <text>.
<text>{text}<text>
"""
```

#### Tactic 2: Poproś o ustrukturyzowany wynik
- JSON, HTML
```python
prompt = f"""
Wygeneruj listę trzech wymyślonych tytułów książek wraz \
z ich autorami i gatunkami.
Podaj je w formacie JSON z następującymi kluczami:
book_id, tytuł, autor, gatunek.
"""
```


#### Tactic 3: Poproś model, aby sprawdził, czy warunki są spełnione
```
text_1 = f"""
Przygotowanie filiżanki herbaty jest łatwe! Najpierw musisz zdobyć \
gotowanie wody. Podczas gdy to się dzieje, \
weź filiżankę i włóż do niej torebkę z herbatą. Gdy woda jest \
wystarczająco gorące, po prostu zalej torebkę z herbatą. \
Odstaw na chwilę, aby herbata mogła się zaparzyć. Po \
kilka minut, wyjmij torebkę z herbatą. Jeśli ty \
na przykład, możesz dodać trochę cukru lub mleka do smaku. \
I to wszystko! Masz pyszne \
filiżanka herbaty do zabawy.
"""
prompt = f"""
Sprawdź tekst znajdujacy się pomiędzy potrójnymi cudzysłowami, czy zawiera sekwencyjną instrukcję. \
Jeżli tak to przepisz tę instrukcje w następującym formacie:

Krok 1 - ...
Krok 2 - …
…
Krok N - …

Jeśli tekst nie zawiera ciągu instrukcji, \
po prostu napisz \"Nie podano żadnych kroków\"

\"\"\"{text_1}\"\"\"
"""
```


#### Tactic 4: Podpowiedź „kilka strzałów”. Daj przykład
```
prompt = f"""
Twoim zadaniem jest udzielenie odpowiedzi w takim samym stylu.

<dziecko>: Naucz mnie cierpliwości.

<dziadek>: Rzeka, która rzeźbi najgłębsze \
dolina wypływa ze skromnego źródła; \
najwspanialsza symfonia powstaje z jednej nuty; \
najbardziej skomplikowany gobelin zaczyna się od pojedynczej nici.

<dziecko>: Naucz mnie o odporności.
"""

```


### Principle 2: Daj modelowi czas na „pomyślenie”

#### Tactic 1: Określ kroki wymagane do wykonania zadania

```
text = f"""..."""

prompt = f"""
Wykonaj następujące czynności:
1 - Streść poniższy tekst oddzielony potrójnymi \
backticks do 1 zdania.
2 - Przetłumacz streszczenie na język francuski.
3 - Wymień każde nazwisko w francuskim streszczeniu.
4 - Wygeneruj obiekt json, który zawiera następujące \
klucze: francuski_podsumowanie, liczba_nazw.

Oddziel swoje odpowiedzi znakami końca wiersza.

Tekst:
```{text}```
"""
```


#### Poproś o output w określonym formacie
```
prompt_2 = f"""
Twoim zadaniem jest wykonanie następujących czynności: 
1 - Podsumuj poniższy tekst znajdujacy się pomiędzy 
  <> jednym zdaniem.
2 - Przetłumacz podsumowanie na Francyski.
3 - Wymień każde nazwisko z przetłumaczonego podsumowania.
4 - Zwróć output json zawierający nastepujące \
  klucze: french_summary, num_names.

Użyj następującego formatu:
Text: <tekst do podsumowania>
Summary: <podsumowanie>
Translation: <przetłumaczone podsumowanie>
Names: <lista nazwisk z przetłumaczonego podsumowania>
Output JSON: <json z kluczami french_summary i num_names>

Tekst: <{text}>
"""
```

#### Tactic 2: Poinstrułuj model jak ma pracować nim zwróci wyniki. 

```python
prompt = f"""
Twoim zadaniem jest ustalenie, czy rozwiązanie ucznia
jest poprawne, czy nie.
Aby rozwiązać problem, wykonaj następujące czynności:
- Najpierw opracuj własne rozwiązanie problemu.
- Następnie porównaj swoje rozwiązanie z rozwiązaniem studenta
i oceń, czy rozwiązanie ucznia jest poprawne, czy nie.
Nie decyduj, czy rozwiązanie ucznia jest poprawne, dopóki
sam rozwiązałeś problem.

Użyj następującego formatu:
Pytanie:
``
miejsce na pytanie
``
Rozwiazanie studenta:
``
miejsce na rozwiazanie studenta
``
Własne rozwiązanie
``
miejsce na kroki, aby opracować własne rozwiązanie i własne rozwiązanie
``
Czy rozwiązanie ucznia jest takie samo, jak własne rozwiązanie:
``
tak lub nie
``
Ocena ucznia:
``
poprawne lub niepoprawne
``

Pytanie:
``
Buduję instalację fotowoltaiczną i potrzebuję pomocy w rozliczeniu finansowym.
- Grunt kosztuje 100 zł za metr kwadratowy
- Panele fotowoltaiczne mogę kupić za 250 zł/metr kwadratowy
- Wynegocjowałem umowę na konserwację, która będzie mnie kosztować  100 tys. zł rocznie i dodatkowo 10 zł/metr kwadratowy
Jaki jest całkowity koszt pierwszego roku działalności w przeliczeniu na metr kwadratowy
`` 
Rozwiązanie studenta:
``
Niech x będzie wielkością instalacji w metrach kwadratowych.
Koszty:
1. Koszt ziemi: 100x
2. Koszt panelu słonecznego: 250x
3. Koszt utrzymania: 100000 + 10x
Całkowity koszt: 100x + 250x + 100000 + 10x = 360x + 100000
``
Własne rozwiazanie:
"""
```
