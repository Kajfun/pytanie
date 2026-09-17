# MODUŁ 3 — Aplikacje mobilne w Claude Code (notatki z transkrypcji)
Projekt: "Nawykometr" — tracker nawyków z grywalizacją. Stack: React Native + Expo + NativeWind
+ Supabase + EAS. Repo dokumentacji: AIBiz-Automatyzacje/nawykometr-docs (SKLONOWANE, 390 plików).

## #2 Co budujemy — PRD i skill /zroastuj-mnie   ⭐ NAJLEPSZA LEKCJA MODUŁU
- Punkt wyjścia: prd.md jako ŹRÓDŁO PRAWDY, wygenerowany wcześniej skillem brainstorm.
- Mechanika produktu: cele tygodniowe -> check-in -> punkty (baza 5 × difficulty 1-3 × energia)
  -> Perfect Week podnosi energię +0,2 (zakres 1,0-3,0) -> progi -> ewolucja potworka "Pixie".
- ⭐⭐ SKILL /zroastuj-mnie — ADWERSARIALNA WERYFIKACJA PLANU PRZED KODOWANIEM.
  Znalazł **11 błędów logicznych**. To jest najcenniejszy wzorzec w całym kursie.
  Przykłady realnych znalezisk (nie kosmetyka!):
   1. MONETYZACJA: kupowanie punktów jest bez sensu — użytkownik może oszukiwać.
      Konsekwencja architektoniczna: liczenie punktów MUSI iść server-side (Supabase edge functions).
      >> To jest poprawka, która gdyby wyszła po napisaniu kodu, kosztowałaby przepisanie backendu.
   2. Subskrypcja z mnożnikiem 1,5× ma WARTOŚĆ MALEJĄCĄ — progi rosną (375 -> 1855 -> 9000),
      więc "za co użytkownik zapłaci w czwartym miesiącu?". Autor próbuje podbić do 2×,
      Claude kontruje, że to tylko szybciej doprowadzi do końca contentu. >> REALNY DIALOG PRODUKTOWY.
   3. INTEGRALNOŚĆ DANYCH: co po reinstalacji / zmianie telefonu? Brak tabeli-źródła prawdy.
   4. STREFY CZASOWE: cron liczy tydzień w niedzielę 23:59, ale użytkownik loguje się z innej strefy.
      Rekomendacja: liczenie client-side przy otwarciu apki wg zegara urządzenia.
      >> KLASYCZNY BŁĄD, KTÓRY WYCHODZI DOPIERO NA PRODUKCJI.
   5. Twardy limit odznaczeń (cel 3×/tydzień, a user odznacza 4. raz -> ekstra punkty).
   6. Sprzeczność w dokumentacji: cele miesięczne vs tygodniowe — wycięte.
   7. Brak contentu po ostatnim poziomie (flaga na przyszłość: 12 poziomów).
   8. Mieszanie metryk: bonus za otwarcie apki + bonus za nawyk = nie rozdzielisz zaangażowania
      od jakości nawyku.
   9. Font Pixel Sans nieczytelny — zostawić tylko do nagłówków.
- ⭐ AUTOR NIE ZGADZA SIĘ ZE WSZYSTKIM: odrzuca propozycję złagodzenia Perfect Week do 80%
  (zostaje all-or-nothing), odrzuca logowanie bez konta (bo kurs ma pokazać autoryzację).
  >> TO JEST WŁAŚCIWA POSTAWA. Roast to wejście do dyskusji, nie wyrocznia. Warto podkreślić.
- ⭐ CYTAT KLUCZOWY: "to jest poniekąd trochę nudna robota, siedzicie przy komputerze około godzinkę
  (...) ale obiecuję, że zaprocentuje tym, że potem będzie mniej błędów i wracania się w budowie
  aplikacji do wcześniejszych etapów. Im lepiej przygotujemy mu kontekst i dokumenty, tym potem
  możemy liczyć na lepsze wykonanie."
  >> TO JEST TEZA CAŁEGO KURSU I JEST PRAWDZIWA. Godzina planowania > dzień debugowania.

## #6 Budowa etapu E1 — autopilot i review   ⭐ RDZEŃ PIPELINE'U
- SEKWENCJA: /dev-plan (plan etapu) -> /dev-docs <ścieżka-planu.md> (rozbicie na zadania)
  -> folder docs/active/ z 3 plikami: kontekst, plan, zadania -> /dev-autopilot-wf (wykonanie).
  UWAGA: do /dev-docs podaje się plik .md, nie folder — sam znajdzie referencje.
- PRACA NA BRANCHU, nie na main. Merge dopiero po przejściu review. >> STANDARD, dobrze że jest.
- ⭐ WARIANT "WF" autopilota = oparty na Dynamic Workflows zamiast na łańcuchu skilli.
  Autor: "mamy większą kontrolę od strony wykonywania instrukcji". Workflow zaczyna od WALIDACJI
  (czy dobry branch, czy są pliki konfiguracyjne) — bramka wejściowa. >> DOBRY WZORZEC.
- ⏱ CZAS: autopilot etapu E1 działał **2,5 godziny** bez nadzoru.
- ⭐⭐ NAJWAŻNIEJSZY WNIOSEK CAŁEGO KURSU O JAKOŚCI:
  Po autopilocie (który zawiera WŁASNE review przez 5 subagentów!) uruchomiono CodeRabbit
  na pull requeście — i CodeRabbit znalazł **6 kolejnych błędów**, w tym oznaczone jako "major".
  >> AGENT SPRAWDZAJĄCY SAM SIEBIE NIE WYSTARCZA. Niezależne, zewnętrzne review łapie to,
     czego pętla self-review nie widzi. To jest twardy, empiryczny argument — i kurs go dostarcza.
     W dokumencie wyeksponować: to jest najmocniejszy dowód w materiale.
- Technika obsługi uwag z review (BARDZO DOBRA, warta skopiowania):
  "spójrz na uwagi od CodeRabbit, uporządkuj od najpoważniejszych do najmniej poważnych, wskaż
   przy każdym co naprawić i opisz każdy jednym zdaniem — tak jakbyś tłumaczył biznesowi,
   dlaczego musimy to poprawić"
  >> Rozwiązuje problem nietechnicznego użytkownika, który dostaje 6 uwag i nie wie, co z nimi zrobić.
  >> Claude weryfikuje też ZASADNOŚĆ uwag bota — nie wykonuje ich ślepo. Właściwe podejście.
- ⭐ "ZADANIA OPERATORA" — autopilot zostawia listę rzeczy do ręcznej weryfikacji przez człowieka.
  Autor zawsze dopytuje: "czy jako operator mam jeszcze jakieś zadania do sprawdzenia?".
  >> ŚWIETNY WZORZEC. Autonomia z jawnie zaznaczoną granicą tego, czego agent nie zweryfikuje sam.
- REALNE BŁĘDY PO DRODZE (uczciwie pokazane, bez cięcia):
  zajęty port 8081 -> błąd; zła komenda buildu (profil iphone zamiast simulator);
  błąd połączenia przy buildzie Android (retry pomógł); poważniejszy błąd naprawiany ~10 minut;
  błąd przy zapisie sekretu do EAS (trzeba spacji zamiast entera przy wyborze środowiska).
  >> To jest wartość transkrypcji, której nie ma w opisie lekcji: ile realnie idzie na dłubaninę.
- Narzędzia wpięte na starcie (świadomie, zanim powstanie kod): Sentry (monitoring), CodeRabbit (review).
  Test Sentry: agent celowo wywołuje błąd, sprawdzamy czy trafił do panelu. >> DOBRA PRAKTYKA.
  Uwaga autora: "Sentry można podpiąć do Claude Code, żeby sam wszedł, zobaczył gdzie jest błąd,
  przeczytał źródło i naprawił" — pętla produkcja -> agent.
- Sekrety do chmury EAS: `eas env:create` (nazwa, wartość, sensitive/secret, środowisko production).
  Na tym etapie tylko Sentry auth token (do source maps); Supabase dopiero później.
- CodeRabbit: płatny (jest trial), konfiguracja przez plik .coderabbit.yaml, commit na MAIN nie na branch.
  Autor: "krok opcjonalny, ale mocno rekomendowany jako druga para oczu".
  Review trwało "kilkadziesiąt minut".

## #3 PRD -> podział na fazy (drugi roast)   ⭐ RÓWNIE WAŻNA
- ⭐ ROZRÓŻNIENIE NARZĘDZI (nieoczywiste, warte zapamiętania):
    skill `brainstorm`  -> do PODZIAŁU PRD NA ETAPY (całościowy widok)
    skill `dev-plan`    -> dopiero POTEM, dla POJEDYNCZEGO etapu (z linkami do Figmy i widoków)
  Autor mówi wprost: "świetnie sprawdza się skill brainstorm, i o dziwo NIE skill dev-plan".
- Metafora: gotowy projekt to klocki Lego; z PRD wyodrębniamy klocki.
- Claude ZAKWESTIONOWAŁ prośbę autora: "onboarding + splash wymagają fundamentów (autoryzacja,
  design system, nawigacja, model danych)". Autor: "świetnie, bo to jest jego rola" i wybiera fundamenty.
  >> DOBRY MOMENT: agent koryguje kolejność narzuconą przez człowieka. Warto to eksponować.
- Wybór kształtu etapów: "każdy etap = ukończony, działający flow na telefonie" (pionowe plastry)
  zamiast warstw poziomych (najpierw cała warstwa danych, potem logika, potem UI).
  >> TO JEST DOBRA DECYZJA ARCHITEKTONICZNA. Pionowe plastry dają szybszy feedback.
- Wynik: 11 etapów. Autor kazał ROZBIĆ etap E0 (wszystkie asety naraz) i rozdzielić asety
  "just in time" do etapów, które ich potrzebują. >> ROZSĄDNE, unika wąskiego gardła na starcie.
- ⭐ DRUGI ROAST (na planie faz, nie na PRD) — **15 luk** w trzech poziomach:
  TIER 1 (ryzyko dużego reworku):
   1. Usuwanie celu kasuje historię -> zamiast usuwania: ARCHIWIZACJA.
   2. ⭐ OFFLINE: apka projektowana pod online, offline dopiero w E5 -> STRUKTURA DANYCH BĘDZIE INNA.
      Naprawa: fundamenty offline wpleść od razu w E3.
      >> TO JEST KLASYCZNY, KOSZTOWNY BŁĄD ARCHITEKTONICZNY. Znalezienie go przed kodem = duża oszczędność.
   3. "Obiecujemy mierzyć sukces, ale nie mamy czym" — brak analityki (retencja 7/30 dni, progresja
      poziomów) i brak crash reportingu. Naprawa: monitoring + anonimowa analityka w E3.
      Autor dopytał, czy Sentry i PostHog to dobre wybory -> potwierdzenie.
   4. Expo Go nie obsługuje modułów natywnych -> trzeba EAS (chmura Expo). "Długi proces na początku,
      sporo błędów, ale raz przejdziesz i potem łatwiej".
   5. Skąd apka wie, czy poprzedni tydzień był udany, gdy user nie otwierał jej 3 tygodnie
      i po drodze zmienił cel/trudność? Naprawa: cotygodniowe MIGAWKI (snapshoty) + blokada edycji targetu.
  TIER 2: cel dodany w środku tygodnia liczony jako nieudany -> liczyć od pełnego tygodnia;
      wielojęzyczność wycięta z MVP; poprawki opisowe.
  TIER 3 (5 drobiazgów): strefy czasowe i zmiana zegara, limit powiadomień iOS, pusty onboarding
      przy pierwszym logowaniu, ocena energii po długiej nieobecności, brak internetu.
- ⭐⭐ NAJLEPSZA POJEDYNCZA WSKAZÓWKA UX CAŁEGO KURSU:
  Gdy roast pisze zbyt technicznie: "potraktuj mnie jako osobę z biznesu, której musisz wytłumaczyć,
  dlaczego coś jest błędne, i daj propozycje naprawy".
  >> Działa uniwersalnie, nie tylko tu. Do wyeksponowania.
- ⭐ CYTAT O GRANICACH: "To nie jest tak, że ten skill znajdzie wszystko w 100%. NIE. Ale nawet jak
  znajdzie kilka błędów i je wyeliminuje, to potem stracę mniej czasu na naprawę."
  oraz: "to WY macie trzymać łapkę na tym wszystkim i kwestionować status quo tego, co on wymyśli.
  To narzędzie też się po prostu myli."
  >> UCZCIWE POZYCJONOWANIE. Kurs nie sprzedaje magii. To podnosi jego wiarygodność.
- Autor zauważa, że tych zawiłości logicznych "sami byście nie rozstrzygnęli — wychodziłyby dopiero
  przy testowaniu". >> I to jest realna wartość adwersarialnego planowania.

## #10 Etapy E4+E5 — energia, historia, synchronizacja   ⭐ NAJLEPSZY OBRAZ REALNEJ PRACY
- Autor ŁĄCZY dwa etapy, uzasadniając to merytorycznie ("to jeden temat, nie dwa — oba odpowiadają
  na pytanie, co się dzieje, kiedy apka wraca do gry po przerwie") + pragmatycznie (długość kursu).
- Mechanika energii — przemyślana i dobrze uzasadniona:
    energia NA KONTO, nie na cel; zakres 1,0-3,0; krok ±0,2 za (nie)idealny tydzień
    nowy cel ma TYDZIEŃ NA ROZBIEG (nie liczy się do Perfect Week)
    tydzień bez żadnego celu -> pomijany, energia stoi
    nieobecność 5 tygodni przy 2,0 -> spadek do podłogi 1,0
    ⭐ PUNKTY IDĄ TYLKO W GÓRĘ, maleje wyłącznie TEMPO ich zdobywania
       (uzasadnienie: punkty są za nawyk, już zasłużone; energia definiuje szybkość rozwoju)
    bonus za wejście: 2 pkt PŁASKO, bez mnożnika — inaczej user dobiłby do końca contentu samym klikaniem
    (sufit 730 pkt/rok)
  >> To jest porządne projektowanie systemu ekonomii w grze. Warte pokazania jako przykład,
     jak rozmawiać z agentem o decyzjach produktowych, a nie tylko o kodzie.
- Historia NIE dostaje własnej zakładki — ląduje przy celu i przy postaci. Uzasadnienie: nie mnożyć widoków.
- ⭐ KOLEJNOŚĆ PRACY (powtarzalna, to jest RDZEŃ METODY KURSU):
    brainstorm (podział na etapy) -> asety/makiety -> decyzje produktowe -> /dev-plan
    -> /dev-docs -> BRAMKA (sprawdzenie środowiska E2E) -> autopilot -> checklista operatora
    -> przegląd wizualny -> PR -> review -> poprawki -> merge
- ⏱ CZAS: autopilot dwóch etapów naraz — autor mówi najpierw "10-11 godzin", potem koryguje do ~5-6.
  "jeszcze w nocy pracował". >> Realna skala: etap = godziny, nie minuty.
- Effort: autor włącza ULTRA przed budową planu. "Zżera dużo tokenów, ale przy układaniu planu
  na dwa etapy naraz robi różnicę. Nie chodzi o kod, chodzi o ułożenie planu."
  >> DOBRE ROZRÓŻNIENIE: wysoki effort na PLANOWANIE, nie na klepanie kodu.
- Wspomina model "Fable" i że wyczerpał na nim kredyty -> spadł na Opusa.
  >> Potwierdza, że materiał jest stosunkowo świeży, ale nazwy modeli i tak trzeba weryfikować.
- Plan dla dwóch etapów: ~1000 linii.
- Kontekst w sesji dobił do ~400 tys. przed uruchomieniem autopilota. Autor chciał wyczyścić,
  ale workflow już wystartował. >> Drobna, ale realistyczna niedbałość.
- ⚠⚠ ISTOTNA AWARIA: **autopilot wykonał wszystkie migracje bazy na BAZĘ TESTOWĄ (E2E),
  a nie na deweloperską.** Autor: "w etapie operatora, to była cała faza A, NIE ZAUWAŻYŁEM TEGO".
  Wychwycił to dopiero Claude przy podsumowaniu.
  >> TO JEST NAJWAŻNIEJSZY PRZYPADEK BŁĘDU W CAŁYM KURSIE. Autonomiczny agent pomylił środowiska
     bazodanowe. Gdyby pomylił je w drugą stronę (produkcja zamiast testów) — byłaby katastrofa.
     W DOKUMENCIE: to jest argument za twardym oddzieleniem środowisk i za tym, żeby agent
     NIGDY nie miał poświadczeń do produkcyjnej bazy.
- ⭐ CHECKLISTA OPERATORA — dojrzały wzorzec, rozbita na sekcje A-G:
    A: migracje/seed (agent), B: regresja E2E (agent, "nic do zrobienia, robię w całości"),
    C: PRZEGLĄD WIZUALNY (człowiek na symulatorze), D: urządzenie fizyczne, E: Android,
    F: zadania agenta, G: weryfikacja analityki
  Autor świadomie SKREŚLA punkty D i E ("nie będziemy testować manualnie na Androidzie").
  >> Uczciwe: pokazuje, że w praktyce się tnie zakres testów. Ale to też jest dług.
- ⭐ ŚWIETNY PROMPT OPERATORSKI: "Pokieruj mnie, co mam zrobić. Jeżeli część punktów możesz sam
  odhaczyć, wykonując polecenia w konsoli, to zrób to. Moją pracę sprowadź do minimum,
  abym mógł jak najszybciej przejść do sekcji C."
  >> Wzorzec delegacji: agent robi wszystko, co da się zautomatyzować, człowiek dostaje tylko to,
     czego nie da się zweryfikować programowo (ocena wizualna).
- ⭐ CIEKAWY PROBLEM I ROZWIĄZANIE: trzech stanów UI (Perfect Week, Daily Login toast, wskaźnik sync)
  NIE DA SIĘ zobaczyć na koncie deweloperskim — wymagają domkniętego tygodnia, świeżego dnia albo
  trwają ułamek sekundy. Claude proponuje przełączenie na środowisko E2E i wygenerowanie tych stanów.
  >> Realny problem testowania stanów zależnych od czasu. Dobre, że kurs go pokazuje.
- Weryfikacja końcowa: 3 nowe eventy w PostHogu (weekend_closed, energy_change, daily_login_bonus_granted)
  sprawdzone w panelu Activity. >> Domknięcie pętli: kod -> zdarzenie -> panel analityczny.

## #4 Pierwsze asety — design.md, Mobbin, Kie.ai, Figma MCP
- ⭐ WZORZEC OTWIERAJĄCY: "zanim puszczę Claude'a samopas, muszę wiedzieć, co MU DOSTARCZYĆ,
  żeby mógł ten etap wykonać. Są rzeczy, których sam nie zrobi."
  Prompt: "wypisz mi, co muszę przygotować od strony graficznej, abyś wykonał etap pierwszy".
  Odpowiedź rozbita na: CO MUSZĘ / CO JUŻ MAM / CO NIE BLOKUJE, ALE PRZYSPIESZY.
  >> DOSKONAŁY, UNIWERSALNY WZORZEC. Odwraca kierunek: nie "zrób", tylko "czego ode mnie potrzebujesz".
- ⭐⭐ PLIK design.md — koncepcja Google Labs (github.com/google-labs-code/design.md, SKLONOWANE).
  PROBLEM, KTÓRY ROZWIĄZUJE (świetnie opisany w lekcji):
    "Zlecasz agentowi zbudowanie ekranów. Robi ładnie. Następnego dnia każesz dorobić drugi ekran
     i nagle przyciski mają inny odcień niebieskiego, inne zaokrąglenia, inne odstępy.
     AGENT NIE PAMIĘTA SWOJEGO STYLU MIĘDZY SESJAMI — za każdym razem zgaduje od nowa."
  ROZWIĄZANIE: brand book napisany pod AI, czytany przed każdą sesją. Dwie warstwy:
    twarde dane (kolory, czcionki, rozmiary, odstępy) + uzasadnienia (skąd te wartości, kiedy używać).
  >> TO JEST NAJLEPSZY POJEDYNCZY POMYSŁ W MODULE MOBILNYM i przenosi się na KAŻDY projekt z UI,
     w tym webowy. Bezpośrednio stosowalne poza kursem.
  getdesign.md — katalog gotowych design.md dużych firm (Airbnb, Figma, Intercom, BMW, HP...).
- design_preview.html — Claude generuje wizualny podgląd design.md, żeby dało się go obejrzeć,
  a nie tylko czytać. >> Prosty, bardzo dobry trik.
- Mobbin + Mobbin MCP — biblioteka screenów z prawdziwych apek jako źródło inspiracji,
  przeszukiwana z poziomu agenta. PŁATNA (kwartalnie). W demo: 48 screenów -> analiza -> design.md.
- ⭐ PIPELINE SPLASH SCREENA (pomysłowy, warty odnotowania):
    5 koncepcji tekstowych -> wybór 3 -> generowanie PIERWSZEJ i OSTATNIEJ KLATKI w 3 silnikach
    obrazu (Nano Banana 2 / Pro / GPT Image-2) = 9 grafik -> raport HTML do porównania -> wybór 2
    -> prompt po angielsku do modeli text-to-video -> 4 silniki wideo (SVD 1.1, Vana 2.7, Kling 3.0,
    Veo 3.1) przez Kie.ai -> 8 wariantów animacji -> wybór -> Claude przycina do 2 s, usuwa dźwięk,
    kompresuje i zapisuje do assets/
  >> Technika "pierwsza + ostatnia klatka -> model wideo interpoluje" jest sprytna i przenośna.
  >> Raport HTML do porównania wariantów — dobry wzorzec przy każdym wyborze spośród N opcji.
- Figma MCP: `claude plugin install figma` + autoryzacja. Claude generuje w Figmie komponenty
  na podstawie design.md, ze WSZYSTKIMI wariantami stanów (default, press, disable, primary,
  secondary, danger). >> Sensowne: warianty stanów to dokładnie to, o czym ludzie zapominają.
- ⚠ KOSZTY UKRYTE TEGO MODUŁU: Mobbin (płatny), Kie.ai (płatny), CodeRabbit (płatny),
  Apple Developer 99 USD/rok, Google Play 25 USD jednorazowo, plus subskrypcja Claude.
  Kurs jest uczciwy co do istnienia tych kosztów, ale warto je zsumować w jednym miejscu.

## #9 Etap E3 — analityka, animacje, "serce apki"   ⭐ NAJLEPSZY DOWÓD NA GRANICE AUTONOMII
- ⭐ ANALITYKA PRZED KODEM: "zakładam konto w PostHogu, bo DANYCH NIE DA SIĘ DOROBIĆ WSTECZ".
  Lista **13 zdarzeń** ustalona ZANIM powstała pierwsza linijka kodu.
  >> ZNAKOMITA ZASADA. Instrumentacja to nie dodatek na koniec.
  Pomysł na listę zdarzeń wygenerował Claude: "stwórz listę akcji, które warto śledzić, tak żeby
  dane dawały wartość merytoryczną — gdzie użytkownik się zatrzymuje, czego nie wykonuje".
- PostHog: region UE (nieodwracalny wybór!), plan Free, świadome ODRZUCENIE Session Replay
  i Web Analytics ("wymagałoby trudniejszych rzeczy z przetwarzaniem danych"; apka jest mobilna).
  >> Rozsądna higiena: nie włączaj nagrywania sesji, jeśli go nie potrzebujesz (RODO, koszt, ryzyko).
- Animacje Pixie: 6 sprite'ów statycznych × stany (idle/happy/sad) + ewolucje = kilkanaście animacji,
  silnik Seedance 2.0 przez skill /kie-generate.
  ⚠ PUŁAPKA TECHNICZNA: modele wideo NIE generują przezroczystego tła. Claude musiał napisać skrypt
  w Pythonie do usunięcia tła. Autor: "nie pytajcie mnie, jaki to skrypt, ja też go nie znam —
  po prostu poprosiłem, żeby osiągnął cel i mógł go zwalidować".
  >> Uczciwe, ale to jest esencja ryzyka vibe codingu: działający kod, którego nikt nie rozumie.
- ⚠ AWARIA WORKFLOW: autor zapomniał skonfigurować środowisko testów E2E przed autopilotem,
  a BRAMKA, KTÓRA MIAŁA TO WYKRYĆ, MIAŁA BŁĄD i przepuściła.
  Skutek: autopilot zatrzymywał się, poprawiał, zjadł ogromną ilość kontekstu.
  Autor naprawił strażnika po fakcie. >> Pokazuje, że nawet dopracowany pipeline ma dziury.
- ⭐ ŚRODOWISKO E2E: druga, ODDZIELNA baza w Supabase (Nawykometr-E2E) + osobny plik .env.e2e
  + skill /e2e-setup, który sprawdza czy jest Maestro, czy są zmienne, i prowadzi przez konfigurację.
  >> Właściwa separacja danych testowych od produkcyjnych. (Por. awaria z migracjami w lekcji #10.)
- ⚠ PUŁAPKA: .env.local wskazywał na bazę E2E zamiast deweloperskiej — znów pomyłka środowisk.
  DRUGI RAZ W KURSIE TEN SAM KLASS BŁĘDU. To nie przypadek, to systemowa słabość.
- ⭐⭐ CO ZNALAZŁ RĘCZNY PRZEGLĄD OPERATORA (po autopilocie, po jego własnym review!):
   1. **Cele nie pojawiały się na dashboardzie** — Claude potwierdził: "to prawdziwy bug,
      nie problem z danymi; w bazie cele są zapisane poprawnie". KRYTYCZNA funkcjonalność nie działała.
   2. Punkty nie zostały poprawnie zliczone w bazie deweloperskiej.
   3. Kolejka offline nie wypchnęła się po powrocie internetu (czyli offline-sync NIE DZIAŁAŁ).
   4. Ikonki w pasku nawigacji za małe (kosmetyka).
   5. Brak elementów UI względem makiety z Figmy.
   6. Duplikaty celów i cel testowy do posprzątania.
  >> TO JEST NAJMOCNIEJSZY DOWÓD W CAŁYM KURSIE: autopilot + testy jednostkowe + testy E2E
     + self-review NIE WYŁAPAŁY trzech realnych błędów funkcjonalnych, w tym takiego, że główny
     ekran nie pokazywał danych. Wyłapał je CZŁOWIEK KLIKAJĄCY W APKĘ.
     WNIOSEK DO DOKUMENTU: ręczna weryfikacja operatorska nie jest opcjonalna. Nigdy.
- Metoda pracy przy przeglądzie: checklista z checkboxami, krok po kroku, operator zgłasza
  "ok / nie ok", wysyła SCREENSHOTY, agent odhacza i reaguje. Bardzo dobra ergonomia.
- Test offline zrobiony fizycznie: wyłączenie Wi-Fi na Macu, odhaczenie celu, próba archiwizacji,
  włączenie sieci, sprawdzenie synchronizacji. >> Porządny test, nie udawany.
- Rebuild wymagany po dodaniu nowych bibliotek natywnych (nie wystarczy odświeżenie serwera).
- Weryfikacja PostHog: zakładka Activity, persona, properties (iOS, wersja systemu), eventy.
