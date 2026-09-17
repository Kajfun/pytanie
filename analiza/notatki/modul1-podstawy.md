# MODUŁ 1 — Claude Code od podstaw (notatki z transkrypcji)
Autor: Kacper Trzepieciński / Akademia Automatyzacji

## #1-1 Claude Code — co to?
- Teza: CC to agent w pętli, nie chatbot. Instrukcja systemowa + narzędzia + pętla do zakończenia zadania.
- "Nie opiera się na architekturze RAG" — eksploruje pliki zamiast bazy wektorowej. TRAFNE.
- Różnice vs aplikacja Claude: terminal vs GUI, eksploracja całego folderu vs wgrane pliki,
  edycja plików na dysku vs kopiuj-wklej, autonomia + to-do lista vs reakcja na prompt.
- Twierdzi: desktop ma MCP + skille; terminal dodatkowo subagenci, hooki, to-do, pluginy.
  >> DO WERYFIKACJI: podział feature'ów desktop vs terminal mógł się zmienić.
- Cennik: brak CC na koncie darmowym. Pro $17, Max x5 $100, Max x20 $200.
- Mocna teza: API 5-6x droższe niż subskrypcja. Autor: plan Max x20 ($200), a zużywa
  "czasem 2-3 tysiące dolarów" kredytów miesięcznie. To jest KLUCZOWY argument ekonomiczny kursu.

## #1-2 Instalacja
- Mac/Linux: curl -fsSL https://claude.ai/install.sh | bash
- Windows: irm https://claude.ai/install.ps1 | iex — WYMAGA Gita (sprawdź `git --version`)
- Znany bug Windows: komenda `claude` nierozpoznana -> dodać ~/.local/bin do PATH (gotowy snippet PowerShell)
- Pierwsze uruchomienie: wybór motywu -> metoda logowania (1 subskrypcja / 2 klucz API / 3 Bedrock itp.)

## #1-3 Sposoby pracy
- Terminal, wtyczka VS Code, aplikacja desktop (zakładka Code), claude.ai (zakładka Code), aplikacja mobilna.
- WAŻNE: wersja webowa NIE pracuje na plikach lokalnych — tylko repozytoria (np. GitHub).
- Kurs skupia się na VS Code + terminal.

## #1-4 Rozpoczęcie projektu
- VS Code + oficjalna wtyczka "Claude Code for VS Code".
- Praktyka: root folder na wszystkie projekty. Powód podany wprost: CC może edytować/usuwać pliki,
  więc NIE uruchamiamy go na pulpicie ani w katalogu systemowym. >> DOBRA, SENSOWNA RADA.
- Autor rekomenduje terminal zamiast wtyczki (wtyczka pokazuje mniej informacji, słabsza customizacja).
- claude-powerline (pasek statusu, plugin społeczności):
    /plugin marketplace add Owloops/claude-powerline
    /plugin install claude-powerline@claude-powerline
    (reload plugins)
    /powerline
- Pasek pokazuje: katalog, model, tokeny sesji, dzienne wydatki, % zużycia kontekstu, nazwę repo.
- >> DO WERYFIKACJI: autor mówi "milion to jest okno kontekstowe". Sprawdzić realny rozmiar okna.

## #1-5 Pierwsze kroki — tryby i uprawnienia  [NAJGĘSTSZA LEKCJA MODUŁU]
- Demo: apka do usuwania tła ze zdjęć (przewija się przez cały kurs).
- Wzorzec pracy: prompt -> CC sprawdza folder -> proponuje warianty -> zadaje pytania -> dopiero buduje.
- Pytania pogłębiające: lista opcji + możliwość wpisania własnej odpowiedzi (opcja 5).
  Dobry przykład w lekcji: agent zrobił słaby research (twierdzi, że kie.ai nie ma endpointu),
  użytkownik go koryguje linkiem do dokumentacji. >> UCZCIWE POKAZANIE, ŻE AGENT SIĘ MYLI.
- TRYBY (Shift+Tab): domyślny -> Accept Edits On -> Plan Mode On (+ Bypass Permissions przy fladze YOLO)
- Plan Mode NIE wykonuje akcji — trzeba wyjść z trybu, żeby cokolwiek zrobił. Autor pokazuje tę pułapkę.
- Plany zapisują się w GLOBALNYM katalogu ~/.claude/plans/, nie w projekcie.
- Uprawnienia: .claude/settings.local.json, sekcja permissions. Komenda /permissions (allow/ask/deny).
- Nadawanie zgód JĘZYKIEM NATURALNYM ("dodaj WebFetch do allow") — CC sam edytuje plik.
- TRZY POZIOMY USTAWIEŃ:
    ~/.claude/settings.json          — globalny (user, wszystkie projekty)
    .claude/settings.json            — projektowy (commitowany, dla zespołu)
    .claude/settings.local.json      — lokalny (gitignored, tylko ja)
  Autor mówi "w hierarchii z local wczytują się w pierwszej kolejności". >> DO WERYFIKACJI kolejność precedencji.
- /model: "Opus 4.6, Sonnet 4.6, Haiku 4.5" >> PRZESTARZAŁE, dziś rodzina Claude 5. FLAGA #1.
- Thinking effort: strzałki lewo/prawo, wyższy = więcej tokenów przed akcją.
- Tryb YOLO: claude --dangerously-skip-permissions -> "Bypass Permissions On".
  Autor mówi wprost, że sam często tak pracuje.
  >> KRYTYCZNE: to jest rada wysokiego ryzyka podana bez adekwatnego ostrzeżenia.
     Dziś istnieje bezpieczniejsza alternatywa (auto mode) — SPRAWDZIĆ i skontrastować.

## #1-6 GIT
- Motywacja podana mocno i trafnie: "największa pułapka pracy z agentami AI bez kontroli wersji".
  Metafora: commit = zapis stanu gry. >> DLA NIETECHNICZNYCH BARDZO DOBRE.
- Dodatkowy argument (często pomijany gdzie indziej): z gitem Claude WIE co się zmieniło,
  może porównać stan wcześniejszy i obecny. To realna korzyść dla agenta, nie tylko dla człowieka.
- Kolejność: repozytorium jako PIERWSZA rzecz przy nowym projekcie.
- GitHub CLI: brew install gh (Mac) / winget install --id GitHub.cli (Win), gh auth login.
  CC zakłada repo prywatne sam, bez wchodzenia na github.com.
- Słowniczek: repozytorium / commit / push / .gitignore.
- Praktyka migracji sekretu: klucz API z localStorage przeglądarki -> .env + .gitignore.
  >> DOBRY MOMENT DYDAKTYCZNY: autor najpierw świadomie robi źle, potem pokazuje naprawę.
- Nawyk: commit + push po KAŻDEJ zmianie.
- UWAGA: autor pracuje w tej lekcji na Bypass Permissions. Spójne z jego stylem, ale ryzykowne.

## #1-7 Pamięć i inżynieria kontekstu   [NAJWAŻNIEJSZA LEKCJA CAŁEGO MODUŁU]
- Definicja context engineering vs prompt engineering — prompt to podzbiór. TRAFNE i dobrze wyjaśnione.
- context window: autor twierdzi "Opus 4.1 i 4.6 pracuje na milionowym oknie", 1M tokenów.
  >> FLAGA #2 — SPRAWDZIĆ. To determinuje całą resztę rad o kontekście.
- /context — rozbicie zużycia: system prompt (~7k), system tools, MCP tools, custom agents,
  memory files, skills, messages, compact buffer, free space. >> BARDZO PRZYDATNE, AKTUALNE.
- CONTEXT ROT: jakość spada po przekroczeniu progu. Autor: sweet spot 30-40%, "w mojej ocenie 50-60%".
  >> Zjawisko realne. Konkretne liczby to jego szacunek, nie twarde badanie — oznaczyć jako heurystykę.
- ZŁOTA ZASADA KURSU: "jedno zadanie na kontekst" -> /clear po każdym zadaniu.
  >> TO JEST NAJLEPSZA POJEDYNCZA RADA W CAŁYM KURSIE. Tania, skuteczna, uniwersalna.
- /compact — kompresja zamiast czyszczenia; można sterować: "/compact skup się na X".
  Autor używa rzadko, tylko przy długich powtarzalnych zadaniach.
- CZTERY WARSTWY PAMIĘCI:
  1. CLAUDE.md — jedyny plik czytany ZAWSZE. Tworzony przez /init.
     Co wrzucać: komendy build/test/deploy, styl kodu, konwencje, pułapki, stack, kluczowe decyzje.
     Czego NIE: tego co Claude odczyta z kodu, podstaw programowania, dokumentacji, opisów plików.
     Limit: 200 linii "według Anthropic". >> SPRAWDZIĆ czy to nadal oficjalna rekomendacja.
     Lokalizacje: globalny ~/.claude/, projektowy, CLAUDE.local.md (autor sam nie używa).
     Może leżeć w katalogu głównym LUB w .claude/ — bez różnicy.
     KIEDY tworzyć: dopiero gdy jest działający kod, nie na pustym folderze. >> ROZSĄDNE.
     Aktualizacja: "zaudytuj CLAUDE.md po większych zmianach".
  2. .claude/rules/ — folder na reguły wyprowadzone z CLAUDE.md gdy ten puchnie.
     SZTUCZKA: frontmatter z argumentem `path` -> warunkowe wczytywanie tylko w danym folderze.
     >> TO JEST NAJCENNIEJSZY NIEOCZYWISTY TRIK W MODULE. Sprawdzić składnię w dokumentacji.
     Też globalny lub projektowy.
  3. auto memory — "od wersji 2.1.59", zapis do GLOBALNEGO folderu, użytkownik nie widzi i nie kontroluje.
     memory.md max 200 linii + podpliki (np. debugging-notes). /memory pokazuje strukturę.
  4. auto dream — porządkuje pliki auto memory między sesjami: dodaje/usuwa/aktualizuje.
     Autor sam mówi: "jeszcze nie działa oficjalnie tak jak deklarują, są to pewne wycieki".
     >> FLAGA #3: lekcja opiera się częściowo na niepotwierdzonych funkcjach. ZWERYFIKOWAĆ.
- Nowy /init interaktywny: CLAUDE_CODE_NEW_INIT=1 claude  >> SPRAWDZIĆ czy nadal potrzebna zmienna.

## #1-8 Komendy i skróty
- /resume (powrót do rozmów), /rename (nazwanie sesji), /rewind (checkpointy tworzone automatycznie
  przez Claude'a - w odróżnieniu od gita, gdzie decydujesz ty), /login, /logout,
  /usage (u autora rzuca błąd), /stats (zużycie per dzień, ulubiony model, najdłuższa sesja),
  /status, /skills, /mcp, /plugin, /config.
- /config: autocompact (autor ma WYŁĄCZONY), Thinking Mode (domyślnie on), Verbose Output,
  theme, Output Style, Chrome, remote control.
- Autocompact: "przy około 950 tysiącach tokenów" >> zależne od flagi #2.
- OUTPUT STYLE — niedoceniony: można kazać Claude'owi mówić mniej technicznie. Dobre dla nietechnicznych.
- Skróty: 2x Esc (czyszczenie), strzałki (historia promptów), Shift+Tab (tryby),
  Shift+Enter (nowa linia; jak nie działa -> /terminal-setup), Ctrl+R (szukanie w historii),
  Ctrl+V (wklejanie zdjęć/screenshotów), 2x Ctrl+D (wyjście).

## #1-9 Pluginy
- Plugin = gotowa paczka rozszerzeń (Anthropic lub społeczność). Analogia do App Store.
- Oficjalny marketplace: "Claude Code Plugin Official", autor podaje 133 pluginy
  (w innej lekcji mówi "ponad 140" — liczba rośnie, nie traktować jako stałej).
- /plugin -> Discovery / Installed / Marketplace / Errors. Search po nazwie.
- Po instalacji ZAWSZE reload plugins.
- SCOPE: user (wszystkie projekty) / project (ten projekt, trafia do repo dla zespołu) / local (tylko ja).
  UWAGA: autor mówi "mamy cztery rodzaje scopów" a wymienia TRZY. Przejęzyczenie.
- Zewnętrzny marketplace: /plugin marketplace add <owner/repo>. Przykład: CodexPluginCC (Codex w CC).
- Wszystkie marketplace'y to pliki Markdown na GitHubie.
