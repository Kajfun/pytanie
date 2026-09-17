# KONSTRUKTOR v2 — analiza makiety (makieta-live.html)

Plik: 1 782 066 znaków, 6088 linii, jeden plik HTML. Sam jest artefaktem Claude
(link `claude.ai/code/artifact/f090af67-...` w stopce). Autor: Krzysztof Brożek, wrzesień 2026.

## CO TO JEST
Produkt dla zajęć robotyki LEGO SPIKE Essential w szkołach. Cztery persony w jednym demo:
  1. DZIECKO (tablet)      — instrukcja krok po kroku, programowanie, odznaki
  2. INSTRUKTOR (telefon)  — kalendarz grup, sesja na żywo, wysyłka na 6 tabletów, zgłoszenia pomocy
  3. ZAPLECZE (przeglądarka) — biblioteka instrukcji, moduły, upload, statystyki, dostęp, zespół
  4. JAK TO DZIAŁA         — widok wyjaśniający przepływ bez ekranów
Plus panel prezentera: 12-krokowy scenariusz demo prowadzący przez wszystkie persony.

## SKALA TREŚCI (nie atrapy — realna zawartość)
  45 instrukcji w 9 modułach tematycznych (Wyścigi, Zima, Święta, Zwierzęta, Wakacje…)
  32 budowle renderowane proceduralnie w izometrii z "klocków"
  3 placówki, 7 grup, 6 dzieci z imionami, 13 historyjek, 35 stron prawdziwej instrukcji (JPEG)
  System: 3 stopnie odznak (brąz/srebro/złoto), 6 poziomów grupy (Odkrywcy → Mistrzowie)

## ⭐ CO JEST MOCNE

### 1. Architektura stanu wyliczanego — i to jest najlepsza decyzja w tym kodzie
Komentarz w kodzie mówi wprost:
  „`log` (historia sesji, od najnowszej) pozostaje JEDYNYM źródłem prawdy o tym, co grupa
   zrobiła — postęp modułów, odznaki i poziom wyliczają się z niego."
  „Poziom liczy się wyłącznie z historii sesji, więc nie da się go »przestawić« w danych —
   rośnie sam, gdy dopiszemy sesję."
Łańcuch: log → groupBadges() → groupPoints() → levelOf(). Jedno miejsce liczy „ile grupa umie".
>> To jest dokładnie ta klasa problemu, którą roast w kursie znalazł w Nawykometrze
   (integralność punktów, przeliczanie tygodni po nieobecności). Konstruktor ma to ROZWIĄZANE
   od startu. To duży plus i warto o tym wiedzieć, zanim ktoś zaproponuje „uproszczenie".

### 2. Komentarze tłumaczą DLACZEGO, z opisem awarii, która je wymusiła
256 bloków komentarzy. Przykłady:
  „Ramka urządzenia leży niżej niż przewodnik. Bez przewinięcia klik w krok zmieniał ekran
   POZA polem widzenia i wyglądało to na zepsuty przycisk."
  „Szprychy tylko pionowa i pozioma — skośnej nie da się złożyć z klocków ustawionych
   równolegle do osi, a »schodki« psułyby rysunek."
  „Bez tego wszędzie wychodziło »3 grup« i »4 budowli«." (przy funkcji odmiany przez liczbę)
>> To jest rzadkie i bardzo wartościowe. Komentarz opisujący objaw błędu jest wart dziesięciu
   opisujących, co robi linijka.

### 3. Higiena techniczna lepsza, niż sugeruje „makieta"
  0 × onclick w HTML — pełna delegacja zdarzeń (23 × .closest)
  esc() poprawnie escapuje & < > " i jest wywołana 108 razy przy 87 przypisaniach innerHTML
  sprawdzona ścieżka user-content (wiadomość instruktora → kidNotify) — escapowana
  251 atrybutów aria-, 15 role= — dostępność była brana pod uwagę
  0 błędów JS przy ładowaniu
  Polska odmiana przez liczbę zrobiona porządnie (1/2-4/5+)

### 4. Dojrzałość produktowa, nie tylko wizualna
  • Test A/B na POJEDYNCZYM KROKU instrukcji — dwa warianty rysunku, mierzony czas do „dalej"
  • „Analiza trudności instrukcji" — krok, na którym grupy najdłużej stoją
  • Prośba o pomoc NAJPIERW autodiagnozuje dwie najczęstsze usterki (lampka huba, kabel),
    dopiero „Nadal nie działa" woła prowadzącego — RAZEM Z LISTĄ tego, co dziecko sprawdziło
  • Rzut podpowiedzi programistycznej na wszystkie tablety naraz, z uzasadnieniem w komentarzu:
    „Najczęstsze zacięcie nie dotyczy klocków, tylko programu: pięć osób naraz nie wie,
     gdzie wpiąć pętlę. Zamiast obchodzić stoliki, prowadzący rzuca gotowy fragment."
  • Zakładka Dostęp: szefostwo ma podgląd BEZ prawa zmiany treści
  • Interfejs świadomy czasu: „trwają zajęcia, zostało 30 min", „za 55 min" — liczone z kalendarza
>> Ktoś to przemyślał od strony sali lekcyjnej, nie od strony ekranu.

## ⚠ RYZYKA PRZED PRZEJŚCIEM NA PRODUKCJĘ (w kolejności priorytetu)

### R1. 79% pliku to 36 wklejonych JPEG-ów (1,4 mln znaków base64)
To jest największy pojedynczy problem. Konsekwencje: brak cache'owania, brak lazy-loadingu,
każda zmiana = ponowne pobranie wszystkiego, 1,78 MB na tablet w szkolnym wifi.
Pierwszy krok migracji: assety do storage (Supabase Storage albo CDN), w kodzie referencje.

### R2. OFFLINE — zadeklarowany w UI, niezaprojektowany w kodzie
Panel pokazuje kafelek „96% dostępność offline / bez przestojów". W kodzie: 0 × localStorage,
0 × fetch, 0 × IndexedDB, 0 × service worker. Cała warstwa danych to stałe w pamięci.
>> ⚠⚠ TO JEST DOKŁADNIE TEN BŁĄD, KTÓRY ROAST W KURSIE ZNALAZŁ JAKO TIER 1 W NAWYKOMETRZE:
   „apka projektowana pod online, offline dopiero w E5 → STRUKTURA DANYCH BĘDZIE INNA".
   Tablety w szkolnej świetlicy to środowisko z realnie złym wifi. Offline-first musi wejść
   do modelu danych NA STARCIE, nie jako etap piąty.

### R3. Czas i strefy czasowe na sztywno
`DEMO_CLOCK = {day:1, min: 14*60+5}` — cała logika „kto ma teraz zajęcia" stoi na zamrożonym
zegarze demo. To poprawne w makiecie. W produkcji: granice dnia, zmiana czasu, sesja trwająca
przez północ. Roast Nawykometru znalazł ten sam problem jako Tier 1.

### R4. Jeden plik, 6088 linii, 141 funkcji, 88 × innerHTML
Dla makiety — w porządku, wręcz zaleta (zero build stepu, otwierasz i działa).
Dla aplikacji — nie do utrzymania. ALE: nie przepisywać w ciemno. Wartość siedzi w modelu
danych i w komentarzach; to trzeba przenieść, nie wyrzucić.

### R5. Brak jakiejkolwiek warstwy trwałości
Zero persystencji to świadoma decyzja makiety, ale oznacza, że CAŁA warstwa danych jest
przed napisaniem: schemat, migracje, autoryzacja (dziecko vs instruktor vs zaplecze vs szefostwo),
synchronizacja tablet↔serwer, rozstrzyganie konfliktów przy pracy offline.

### R6. Kod w czystym ES5 (339 × var, 0 × let/const/arrow/class)
Nie jest to błąd i przy jednym pliku bez build stepu ma sens. Ale przy przejściu na framework
to jest decyzja do podjęcia świadomie, nie do odziedziczenia przypadkiem.

## 🎯 JAK ZASTOSOWAĆ METODĘ Z KURSU KONKRETNIE TUTAJ
Makieta jest w istocie gotowym PRD — bogatszym niż to, co w kursie powstawało przez godzinę
rozmowy. To przeskakuje pierwszy etap kursowego pipeline'u.

KOLEJNOŚĆ, KTÓRĄ BYM ZAPROPONOWAŁ:
  0. Roast na makiecie — nie na wyglądzie, tylko na MODELU DANYCH i na trybie offline.
     Pytania: co się dzieje, gdy tablet traci sieć w połowie sesji? gdy dwoje dzieci
     odznacza ten sam krok? gdy instruktor kończy sesję, a jeden tablet jest offline?
     gdy instrukcja zostanie zmieniona w zapleczu w trakcie trwania sesji?
  1. design.md wyciągnięty z makiety — kolory, typografia, spacing, komponenty JUŻ istnieją
     w tych 95 KB CSS. Spisać je jako źródło prawdy, zanim styl zacznie się rozjeżdżać.
  2. Podział na etapy PIONOWO (działający przepływ end-to-end), nie po personach.
     Sugerowany E1: jedna grupa, jedna instrukcja, tablet↔instruktor, offline w modelu danych.
  3. Assety z base64 do storage — osobny, mechaniczny etap, dobry kandydat na autopilota.
  4. Analityka i monitoring PRZED kodem — makieta już wie, co mierzyć (statystyki, trudne kroki,
     test A/B). Lista zdarzeń jest praktycznie gotowa do spisania.
