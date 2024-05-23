# Bazy danych - projekt

**Proszę o zgłoszenia w przypadku niejasności, ew. problemów i usterek. Zastrzegam sobie prawo do wprowadzania poprawek w treści do czwartku 23 maja włącznie.**

### System do ewaluacji jakości badań naukowych

Naukowcy piszą prace naukowe publikowane na konferencjach.

Każda konferencja ma przypisaną liczbę punktów, którą przyznaje się za każdą opublikowaną na niej pracę: 200, 140, 100, 70 lub 20.
Punktacja dla danej konferencji może się zmieniać w czasie (ale jest stała w ciągu każdego dnia).
Prace mają datę publikacji, tytuł, listę autorów i konferencję, a każdy autor danej pracy ma przypisaną jedną jednostkę naukową (afiliację). W każdej opublikowanej pracy danego autora afiliacja może być inna. Zakładamy, że jeśli autor ma podaną w pracy inną afiliację niż w jakiejś wcześniejszej pracy, to nastąpiła zmiana afiliacji - obowiązująca od daty publikacji nowej pracy.
Jednostka naukowa dostaje punkty za każdą pracę opublikowaną przez osoby afiliowane przy tej jednostce. Punkty są przydzielane następująco:
1. w pracach za 200, 140 i 100 punktów - jednostka otrzymuje całość punktów za pracę, nawet jeśli praca ma również współautorów spoza tej jednostki.
2. w pracach za 70 punktów - *70\*sqrt(k/m)*, gdzie *k* - liczba autorów z danej jednostki, *m* - liczba wszystkich autorów; ale nie mniej niż 7 punktów, zaokrąglenie do 4 cyfr po przecinku
3. w pracach za 20 punktów - *20\*k/m* (*k* i *m* zdefiniowane jak wyżej), ale nie mniej niż 2 punkty, zaokrąglenie do 4 cyfr po przecinku.

Oczywiście dla każdej pracy liczy się punktacja za konferencję ważna w dzień publikacji oraz jednostka autora podana przy tej pracy.

Twoim zadaniem jest wykonanie następującego API:
1. dodawanie prac (może oznaczać zmianę afiliacji dla danego autora),
2. zmiana punktacji konferencji (należy przygotować się na częste zmiany tej punktacji),
3. wyliczenie listy jednostek z sumaryczną punktacją za publikacje w podanym przedziale dat (posortowane malejąco wg liczby punktów),
4. wyliczenie listy autorów z przypisaną sumaryczną punktacją za publikacje w podanym przedziale dat (liczą się pełne punkty za każdą konferencję),
5. wyliczenie szczegółowego dorobku podanego autora.

Specyfikacja techniczna dla każdej operacji jest podana poniżej.

Dane nie będą sprzeczne (w szczególności nie będzie dwóch prac tego samego autora w ten sam dzień z różnymi afiliacjami) ale może się zdarzyć, ze prace nie są dodawane w kolejności chronologicznej.
Funkcje są zawsze wywoływane z poprawnymi parametrami, odpowiednich typów, zgodnie ze specyfikacją poniżej.

*Uwaga: to są znacznie uproszczone zasady ewaluacji, zainteresowani mogą zapoznać się z odpowiednim [rozporządzeniem](https://isap.sejm.gov.pl/isap.nsf/download.xsp/WDU20220000661/O/D20220661.pdf)*.

### Co będzie oceniane?

**Model konceptualny** - powinien składać się z diagramu E-R, komentarza zawierającego więzy pominięte w diagramie.

**Model fizyczny** - powinien być plikiem sql nadającym się do czytania (i oceny) przez człowieka. Powinien zawierać definicję wszystkich niezbędnych użytkowników bazy i ich uprawnień, tabel, więzów, indeksów, kluczy, akcji referencyjnych, funkcji, perspektyw i wyzwalaczy. Nie jest niezbędne wykorzystanie wszystkich tych udogodnień, ale tam, gdzie pasują, powinny być wykorzystywane.

**Dokumentacja projektu**  - ma się składać z pojedynczego pliku pdf zawierającego ostateczny model konceptualny oraz dokładne instrukcje, jak należy aplikację uruchomić. Dokumentacja ma dotyczyć tego, co jest zaimplementowane; w szczególności, nie można oddać samej dokumentacji, bez projektu.

**Program** - oceniany będzie kod źródłowy oraz poprawność i efektywność działania.

Oddajemy archiwum zawierające wszystkie pliki źródłowe programu, dokumentację w pliku pdf, model fizyczny w pliku sql oraz polecenie typu make (ew. skrypt run.sh, itp.) umożliwiające kompilację i uruchomienie systemu.
Projekty należy przygotować indywidualnie.

### Technologie

Projekty będą testowane w systemie Linux (Ubuntu 22.04.4 LTS). Język programowania  – zalecany python 3.  Wybór innego języka wymaga zatwierdzenia przez prowadzącego pracownię. Twój program po uruchomieniu będzie mógł się podłączyć do uruchomionej już w systemie pustej bazy danych PostgreSQL (w wersji >=13, dokładna wersja będzie podana potem).

Twój program po uruchomieniu powinien przeczytać ze standardowego wejścia ciąg wywołań funkcji API, a wyniki ich działania wypisać na standardowe wyjście.

Wszystkie dane powinny być przechowywane w bazie danych, efekt działania każdej funkcji modyfikującej bazę, dla której wypisano potwierdzenie wykonania (wartość OK) powinien być utrwalony. Program może być uruchamiany wielokrotnie np. najpierw wczytanie pliku z danymi początkowymi, a następnie kolejnych testów poprawnościowych. Przy pierwszym uruchomieniu program powinien utworzyć wszystkie niezbędne elementy bazy danych (tabele, więzy, funkcje wyzwalacze) zgodnie z przygotowanym przez studenta modelem fizycznym. Baza nie będzie modyfikowana pomiędzy kolejnymi uruchomieniami. Program nie będzie miał praw do czytania, tworzenia i zapisywania jakichkolwiek plików z jednym wyjątkiem:  program będzie mógł czytać pliki z bieżącego katalogu (np. zawartosć pliku sql ze schematem fizycznym).

### Format pliku wejściowego

Każda linia pliku wejściowego zawiera obiekt JSON (http://www.json.org/json-pl.html). Każdy z obiektów opisuje wywołanie jednej funkcji API wraz z argumentami.

Przykład: obiekt `{ "function": { "arg1": "val1", "arg2": "val2" } }` oznacza wywołanie funkcji o nazwie function z argumentem `arg1` przyjmującym wartość `val1` oraz `arg2` – `val2`.

W pierwszej linii wejścia znajduje się wywołanie funkcji open z argumentami umożliwiającymi nawiązanie połączenia z bazą danych.

**Przykładowe wejście**

```
{ "open": { "host": "localhost", "baza": "student", "login": "student", "password": "qwerty"}}
{ "function1": { "arg1": "value1", "arg2": "value2" } }
{ "function2": { "arg1": "value4", "arg2": "value3", "arg3": "value5" } }
{ "function3": { "arg1": "value6" } }
{ "function4": { } }
```

**Format wyjścia**

Dla każdego wywołania wypisz w osobnej linii obiekt JSON zawierający status wykonania funkcji OK/ERROR/NOT IMPLEMENTED oraz zwracane dane wg specyfikacji tej funkcji.

Format zwracanych danych (dla czytelności zawiera zakazane znaki nowej linii):

```
{ "status":"OK",
  "data": [ { "atr1":"v1",
              "atr2":"v2" },
            { "atr1":"v3",
              "atr2":"v3" } ]
}
```

Tabela `data` zawiera wszystkie wynikowe krotki. Każda krotka zawiera wartości dla wszystkich atrybutów.

**Przykładowe wyjście**

```
{ "status": "OK" }
{ "status": "NOT IMPLEMENTED" }
{ "status": "OK", "data": [ { "a1": "v1", "a2": "v2"}, { "a1": "v3", "a2": "v3"} ] }
{ "status": "OK", "data": [ ] }
{ "status": "ERROR" }
```


### Format opisu API

```
<function> <arg1> <arg2> … <argn> // nazwa funkcji oraz nazwy jej argumentów

// opis działania funkcji

// opis formatu wyniku: lista atrybutów wynikowych tabeli data
```

Należy oddać samodzielnie napisany program implementujący wszystkie funkcje, dokumentację (zawierającą model konceptualny) oraz model fizyczny.

### Nawiązywanie połączenia i definiowanie nowych użytkowników

```
open <host> <baza> <login> <password>
// przekazuje dane umożliwiające podłączenie Twojego programu do bazy 
// - nazwę host-a i bazy, login oraz hasło, 
// wywoływane dokładnie jeden raz, w pierwszej linii wejścia
//
// zwraca status OK/ERROR w zależności od tego czy udało się
// nawiązać połączenie z bazą 

newuser <secret> <newlogin> <newpassword> 
// tworzy użytkownika <newlogin> z hasłem <newpassword>,
// argument <secret> musi być równy d8512346fbc5bb7a325ca4 
//
// zwraca status OK/ERROR 

deluser <secret> <login>
// usuwa użytkownika <login>
// argument <secret> musi być równy d8512346fbc5bb7a325ca4 
//
// zwraca status OK/ERROR 
```

### Operacje modyfikujące bazę

Każde z poniższych wywołań powinno zwrócić obiekt JSON zawierający wyłącznie status wykonania: OK/ERROR/NOT IMPLEMENTED.
Wywołania z podanym niepoprawnymi wartościami `<login> <password>` muszą zwrócić status ERROR i nie mogą się wykonać.
W przypadku gdy nie zaimplmentowaliście jakiejś funkcji (nie zalecane) jej wywołanie musi zwrócić status NOT IMPLEMENTED.


1. dodawanie prac (json: data/tytuł/konferencja/autorzy z afiliacjami) - może oznaczać zmianę afiliacji dla danego autora

```
article <login> <password> <date> <title> <conference> <author list> 
// <date> - data publikacji
// pary <title> <conference> są unikalne, <title> to tytuł pracy
// <conference> jednoznacznie identyfikuje konferencję,
// <author> jest tablicą obiektów postaci:
// [ { <author>, <institution>},
//   { <author>, <institution>},
// ]
// <author> jednoznacznie identyfikuje autora, 
// <institution> jednoznacznie identyfikuje instytucję
```

2. zmiana punktacji konferencji (json: konferencja, data, nowa liczba punktów) - należy przygotować się na częste zmiany tej punktacji :)
```
points <login> <password> <date> <points_list>
// <date> - data, od której obowiązuje zmiana
// <points_list> jest tablicą obiektów postaci:
// [ { <conference>, <points> },
//   { <conference>, <points> },
// ]
// <conference> jednoznacznie identyfikuje konferencję,
// <points> liczba punktów przyznana konferencji

```

### Pozostałe operacje

Każde z poniższych wywołań powinno zwrócić obiekt JSON zawierający status wykonania OK/ERROR, a także tabelę data zawierającą krotki wartości atrybutów wg specyfikacji poniżej.


1. lista jednostek z przypisaną punktacją za publikacje w podanym przedziale dat (posortowane malejąco wg liczby punktów)

```
institution <start_date> <end_date> 

// zwraca listę jednostek wraz z punktacją za publikacje
// w podanym przedziale dat (posortowane malejąco wg liczby punktów)
//
// Atrybuty zwracanych krotek: 
//  <institution> <number of points>
```

2. lista autorów z przypisaną sumaryczną punktacją za publikacje w podanym przedziale dat (w postaci sumy pełnej liczby punktów przypisanych każdej konferencji, na której dana osoba opunlikowała pracę)

```
author <start_date> <end_date> 
// zwraca listę autorów wraz z punktacją za publikacje 
// w podanym przedziale dat (posortowane malejąco wg liczby punktów)
//
// Atrybuty zwracanych krotek: 
//  <author> <number of points>

```

3. szczegółowy dorobek danego autora

```
author_details <author>
// zwraca listę publikacji podanego autora 
// (pogrupowaną wg konferencji i posortowaną malejąco 
// wg liczby punktów przypisanej obecnie danej konferencji, 
// a wewnątrz grupy alfabetycznie tytułami)

// Atrybuty zwracanych krotek: 
//   <conference> <year> <number of points> <institution>  <title>
// gdzie 
//   <conference> identyfikuje jednoznacznie koferencję
//   <year> rok opublikowania pracy
//   <number of points> liczba punktów przypisana podanej konferencji w momencie publikacji danej pracy
//   <institution> afiliacja autora w momencie publikacji danej pracy
//   <title> tytuł pracy
```
