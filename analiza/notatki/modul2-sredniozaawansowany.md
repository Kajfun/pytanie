# MODUŁ 2 — Claude Code średniozaawansowany (notatki z transkrypcji)

## #1 Skille — wprowadzenie
- Demo: wbudowany skill Simplify na apce do usuwania tła. Usunął ~70 linii stylizacji + martwy kod.
- Teza (MOCNA I PRAWDZIWA): "Modele AI mają to do siebie, że jeśli ich nie pilnujemy,
  bardzo komplikują kod, który potem jest ciężej rozbudowywać". >> To jest realny problem vibe codingu.
- Definicja: skill = plik markdown z instrukcją "co robić i kiedy". Metafora: kartka z instrukcją
  dla nowego pracownika, raz napisana, zawsze pod ręką.
- Budowa: frontmatter (klucz: wartość) + instrukcja. OBOWIĄZKOWE: name, description.
  description decyduje, czy Claude sam wybierze skill. >> To jest sedno; słabe description = skill nie odpala.
- Lokalizacja projektowa: .claude/skills/
- Wywołanie: naturalnym językiem (Claude dobiera) LUB jawnie komendą. Autor woli jawnie — kontrola.
- Skill `gemini` — CC odpala Gemini CLI i weryfikuje kod innym modelem. >> CIEKAWY WZORZEC:
  cross-model review. Warto rozważyć niezależnie od kursu.
- Skill DevDocs: bierze plan -> tworzy folder z 3 plikami (zadania rozbite na etapy+podzadania,
  kontekst, notatki). Uzasadnienie: "nie jesteście w stanie tego zbudować w jednej sesji kontekstowej".
  >> TO JEST NAJMOCNIEJSZY ARGUMENT METODOLOGICZNY KURSU i spina się z lekcją #1-7 o context rot.
- Skill DevAutopilot: skill wywołujący INNE skille. Execute -> Review -> (pętla poprawek) -> Complete
  -> Compound (wyciąga błędy, żeby nie powtarzać w przyszłości). Działa 3-4h w tle.
  >> ARCHITEKTONICZNIE NAJCIEKAWSZA RZECZ W KURSIE. Zweryfikować w kodzie repo.
- Repo: github.com/AIBiz-Automatyzacje/claude-code-zasoby (publiczne) — SKLONOWANE.

## #2 Budowanie skilli (Skill Creator)
- Autor deklaruje: "żadnego skilla nie napisałem ręcznie i nie zamierzam". >> UCZCIWE, ale
  oznacza, że jakość jego skilli zależy od generatora. Do zweryfikowania w kodzie.
- Skill Creator = oficjalny plugin Anthropic z marketplace. Tworzy, AUDYTUJE i POPRAWIA skille
  (w tym pobrane z internetu). Bierze aktualną dokumentację.
- ŚWIETNY POMYSŁ: poproś Claude'a, żeby przejrzał projekt, commity i logi sesji i zaproponował,
  jakie powtarzalne czynności opakować w skilla. W demo wyszły 4: rollback, serve, ship, css-audit.
  >> TO JEST DO NATYCHMIASTOWEGO SKOPIOWANIA. Nie wymaga kupowania kursu.
- Zasada: nie szukać skilli na siłę — tylko tam, gdzie jest powtarzalność albo powtarzalny błąd.
- FRONTMATTER — pełniejszy zestaw:
    name        (wymagany)
    description (wymagany) — keywordy zwiększają trafność wywołania
    when_to_use  — gdy description nie wystarcza i skill się nie odpala
    argument_hint — podpowiedź autocomplete dla argumentów
  >> ZWERYFIKOWAĆ nazwy kluczy w dokumentacji; to typowe miejsce na przestarzałą informację.

## #3 Gdzie szukać skilli
- Oficjalny marketplace Anthropic: "ponad 140 skilli" (w module 1 mówił 133 pluginy — liczby płynne).
- skills.sh (~91 tys.), skillsmp.com (~milion), claude-plugins.dev. Sortowanie po pobraniach/gwiazdkach.
- BEZPIECZEŃSTWO — autor podnosi to sam i słusznie: "skille mogą mieć złośliwe instrukcje",
  np. skrypt wysyłający dane z komputera. Weryfikacja: autor, gwiazdki, komentarze, View Source,
  i — dobry trik — wklej link do Claude'a i każ sprawdzić, czy nie ma złośliwych instrukcji.
  >> ROZSĄDNE, ale niewystarczające. Milion skilli z niezweryfikowanego źródła to realna
     powierzchnia ataku (prompt injection). W dokumencie dać mocniejsze ostrzeżenie niż kurs.

## #4 Subagenci   [NAJLEPIEJ WYTŁUMACZONA LEKCJA KURSU]
- Lokalizacja: .claude/agents/
- Demo: agent Security Essential -> audyt bezpieczeństwa. Zużył ~65 tys. tokenów, 3 minuty,
  we WŁASNYM oknie kontekstowym. Do głównego wątku wraca tylko raport.
- ROZRÓŻNIENIE, KTÓRE WARTO ZAPAMIĘTAĆ:
      skill zmienia JAK Claude pracuje   (instrukcja, ładuje się do TWOJEGO kontekstu)
      subagent zmienia KTO pracuje       (osobny pracownik, osobne okno, wraca z raportem)
  >> NAJLEPSZE ZDANIE W CAŁYM KURSIE. Czyste, prawdziwe, użyteczne.
- Kiedy skill: konwencje kodu, styl, wytyczne designu. Kiedy subagent: audyt, przeszukanie 50 plików,
  analiza architektury — czyli gdy nie chcesz zaśmiecić głównego wątku.
- /agents -> running | library. Źródła: User / Project / Plugin / Built-in (Anthropic, nieusuwalne).
- Tworzenie: Create New Agent -> Project|Personal -> Generate with Claude (rekomendowane) | Manual.
- FRONTMATTER SUBAGENTA: name, description, model (domyślnie `inherit`), tools, **skills**
- ⚠ KLUCZOWA PUŁAPKA (autor celowo ją prowokuje w demo):
  SUBAGENT NIE DZIEDZICZY SKILLI Z SESJI. Trzeba je jawnie wymienić w kluczu `skills` we frontmatterze.
  Generator pominął ten klucz i trzeba było poprawić ręcznie.
  >> BARDZO WARTOŚCIOWE. To jest dokładnie ten typ wiedzy, po który się idzie na kurs.
- Ctrl+B -> przeniesienie agenta w tło, główny wątek odblokowany. /agents pokazuje running + progress.
- Wiele subagentów: równolegle (skill dev-docs-review odpala 5 naraz: bezpieczeństwo, wydajność,
  optymalizacja, dobre praktyki) albo łańcuchowo (1->2->3->4->główny wątek).
- Oszczędność: subagentowi można dać tańszy model (Haiku) zamiast dziedziczenia.
  >> KONKRETNA, DZIAŁAJĄCA OPTYMALIZACJA KOSZTU.
- ⚠ Model naming: w tej lekcji "Opus 4.7" i "Haiku 4.0"; w module 1 "Opus 4.6/Sonnet 4.6/Haiku 4.5".
  NIESPÓJNE NAWET WEWNĄTRZ KURSU. Potwierdza, że nazw modeli nie należy brać z materiału.

## #5 Agent Teams (eksperymentalne)
- Architektura: Team Leader (główna sesja) + Teammates (niezależne sesje) + Tasks (współdzielona lista)
  + Mailbox (komunikacja między agentami).
- RÓŻNICA vs subagenci:
    subagent      -> własne okno, raport wraca do callera, główny agent zarządza, krótkie zadania
    team agent    -> CAŁKOWICIE NIEZALEŻNA SESJA, po wyczerpaniu kontekstu RESTARTUJE SIĘ i kontynuuje,
                     agenci komunikują się MIĘDZY SOBĄ, samodzielna koordynacja, złożona praca
- Demo: generator karuzeli graficznych. Zespół: Infra -> Design -> QA z zależnościami (blocked by).
  Całość ~30 minut. Efekt: działa funkcjonalnie, design "do poprawy", grafika nachodzi na przycisk.
  >> UCZCIWE POKAZANIE. Autor nie udaje, że wyszło idealnie.
- Display modes: InProcess (jeden terminal, strzałki lewo-prawo — autor preferuje)
  vs SplitPane (wymaga tmux, "czasami crashuje", "odpaliło się raz na dziesięć prób").
- ⚠⚠ ROZBIEŻNOŚĆ TECHNICZNA — NAZWA ZMIENNEJ:
    transkrypcja mówi:  CLAUDE_CODE_EXPERIMENTAL = 1  oraz  claude.code.experimental.agents
    PDF kursu mówi:     CLAUDE_CODE_EXPERIMENTAL_AGENTS
    oficjalna dokumentacja (sprawdzona): CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
  >> FLAGA KRYTYCZNA. Zła nazwa = funkcja się nie włączy, a użytkownik nie wie dlaczego.
- KOSZT: autor ostrzega wprost — "ekstremalnie token-żerne", na planie Pro limit skończy się
  w połowie zadania. >> DOBRZE, że ostrzega.
- Znany błąd: Claude czasem myli Team Agent z subagentami — trzeba napisać wprost "Team Agent".
- Ciekawy pomysł poza kodem: zespół do analizy konkurencji (LinkedIn / strona / social) z porównaniem.

## #6 Serwery MCP
- Definicja: mały program-pośrednik między CC a zewnętrznym narzędziem, tłumaczy rozmowę.
  Przepływ: CC pyta o listę narzędzi -> serwer wystawia funkcje+parametry -> CC ich używa.
- Demo 1 (Figma): eksport widoku aplikacji do Figmy przez MCP. Uwaga autora: "nie zrozumiał,
  że ma użyć serwera MCP. Tak czasem się zdarza" — trzeba doprecyzować.
- Demo 2 (Supabase): plan -> DevDocs -> devdocs execute -> MCP tworzy tabelę i storage.
  Argument: bez MCP trzeba by ręcznie kopiować SQL do edytora i debugować.
- Instalacja: `claude mcp add <flagi> <nazwa> <komenda/URL>`. Najważniejsza flaga: --scope
- SCOPE (powtórzony trzeci raz w kursie): user / project / local.
- Gdzie szukać: dokumentacja Claude Code (lista popularnych), albo wygoogluj "<narzędzie> MCP".
- /mcp — lista serwerów + autoryzacja (Supabase wymagał "authenticate" -> OAuth w przeglądarce).
- >> UWAGA WŁASNA: serwery MCP zjadają kontekst (widać w /context jako "MCP tools").
   W live'ach autor sam mówi o wyłączaniu zbędnych MCP dla oszczędności. W kursie tego wątku brak tutaj.

## #7 Hooki   [autor: "jeden z najbardziej niedocenianych mechanizmów"]
- Metafora: Claude = pracownik, hook = nadzorca, akcja = efekt w świecie.
- ANATOMIA: event + matcher + command (skrypt) + exit code (może ZABLOKOWAĆ Claude'a).
- Eventy: PostToolUse, SessionStart, SessionEnd, Stop i "dziesiątki" innych.
  Autor otwarcie: "ja nie znam większości tych eventów i nie zamierzam ich znać".
- Lokalizacja: .claude/hooks/ (skrypty) + rejestracja w .claude/settings.json w sekcji hooks.
- Scope: globalny / projektowy / local.
- DEMO 1: hook na event Stop -> telefon dzwoni po zakończeniu zadania. Sensowne przy dev-autopilot
  działającym 3-4h. >> PRAKTYCZNE.
- DEMO 2: blokada dostępu do .env przez hook.
  ⚠⚠ NAJWAŻNIEJSZE ZDANIE: "Jak widzicie, został zablokowany przez hook. **Claude szuka oczywiście
  obejścia.** Natomiast już nas to nie interesuje."
  >> TO JEST OGROMNE. Autor mimochodem przyznaje, że hook nie jest twardą granicą bezpieczeństwa —
     agent próbuje go obejść. Kurs przechodzi nad tym do porządku dziennego. W DOKUMENCIE MUSZĘ
     TO WYPUNKTOWAĆ: hook to higiena i wygoda, NIE model zagrożeń.
- Plugin Hookify (oficjalny marketplace): tworzy hooki z opisu w języku naturalnym.
  Bonus: przeanalizuje historyczne sesje i commity i ZAPROPONUJE hooki pod twój workflow.
  >> DOBRY, TANI TRIK.

## #8 Dynamic Workflows
- Czym się różni: Claude PISZE SKRYPT (JavaScript), który orkiestruje agentów. Nie instrukcja,
  nie delegacja — kod sterujący.
- TABELA SKALI (wartościowa, warto przenieść do dokumentu):
    Claude       — 1 wątek
    Skill        — brak skali (to tylko instrukcja)
    Subagent     — kilkanaście
    Agent Teams  — lider + kilku
    Workflow     — SETKI agentów
- Uruchomienie: słowo kluczowe "workflow" albo "ultracode" (albo effort -> ultracode).
  Domyślnie WŁĄCZONE, nic nie trzeba aktywować. Autor: feature "wyszedł na początku czerwca", beta.
- Nawigacja w panelu: /workflows, Enter (rozwiń), J (dół), K (góra), P (pauza), X (stop/restart).
- Zapis: save -> ląduje w .claude/workflows/ jako plik .js. Wielokrotnego użytku.
- KOSZTY (najkonkretniejsze liczby w kursie):
    3 prostych agentów (bugi/czytelność/martwy kod)  ~150 tys. tokenów
    pokrycie projektu testami: 8 agentów, 15 minut,  ~500 tys. tokenów (~50 tys./agent)
    własne eksperymenty autora:                      "kilkanaście milionów"
  >> BARDZO DOBRE, że podaje liczby. To pozwala policzyć, czy cię na to stać.
- Wniosek autora: narzędzie do DUŻYCH, POWTARZALNYCH zadań, nie do prostych zleceń. ZGODA.

## #9 /goal
- Oficjalna funkcja (w odróżnieniu od Ralpha Wigguma — zewnętrznego dodatku z wcześniejszej lekcji).
- PĘTLA: pracownik (Opus/Sonnet) pisze kod i odpala testy -> po każdej turze
  kontroler (Haiku) sprawdza, czy warunek spełniony -> jeśli nie, kolejna tura.
- Uruchomienie: /goal + zadanie + warunek ukończenia. Startuje od razu, bez osobnego promptu.
- Wskaźnik "Goal Active" z licznikiem czasu w prawym dolnym rogu.
- DOBRY WARUNEK = MIERZALNY KONIEC. "przechodzące testy backendu i frontendu" TAK;
  "ładny widok", "działająca aplikacja" NIE.
- Limit 4000 znaków.
- PAS BEZPIECZEŃSTWA: "zatrzymaj się po 20 turach" albo "po 30 minutach" — inaczej zły warunek
  przepala tokeny w nieskończoność. >> KONKRETNA, DOBRA RADA.
- Autor rekomenduje automode ALBO bypass permissions, żeby nie pytał co chwilę.
  >> WAŻNE: automode to bezpieczniejsza opcja niż bypass. Kurs stawia je obok siebie jak równorzędne.
     W dokumencie rozdzielić i wskazać automode jako domyślny wybór.
- Demo: dodanie wyboru koloru tła + test jednostkowy. 9 minut, zadziałało.
- Kiedy: szybka naprawa błędu, powtarzalne zadanie z jasną metą. Kiedy nie: gdy nie ma mierzalnego końca.
