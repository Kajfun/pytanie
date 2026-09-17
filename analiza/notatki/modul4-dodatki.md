# MODUŁ 4 — Dodatkowe materiały (notatki z transkrypcji)

## Ralph Wiggum Loop
- Nazwa od postaci z Simpsonów. Idea: zamknąć Claude'a w pętli bashowej, dać zadanie, mieli aż zrobi.
- ARCHITEKTURA: pomysł -> PRD (Product Requirement Document) -> rozbicie na 18-40 zadań (User Stories
  z Acceptance Criteria i dependencies) -> pętla wykonawcza, aktualizująca plik postępu.
- Narzędzie: ralph-tui (github.com/subsy/ralph-tui) — wymaga runtime Bun. SKLONOWANE.
- Setup: `ralph-tui setup` -> issue tracker, folder, labele, wybór Claude Code, LICZBA ITERACJI.
  Autor ustawia 40 ("mam konto Max, limity mnie nie interesują"). Auto-commit on task completion: yes.
- Skróty TUI: S start, P pauza, Q wyjście, +/- 10 iteracji, R refresh, L load, D dashboard.
- WAŻNE: zadania NIE idą po kolei — agent sam układa kolejność wg zależności.
- Demo: gra mobilna (quiz + łapanie zdrowego jedzenia), React Native, 18 zadań, **53 minuty**.
  Test przez Expo Go (skan kodu QR). Autor: "10 minut przebrnięcia przez to wszystko".
- ⚠ UCZCIWE: Claude nie umiał sam uruchomić `npx expo start` — autor musiał wpisać ręcznie.
- Pozycjonowanie: "punkt początkowy", nie produkt końcowy. >> ZGODA, i to jest właściwa rama.
- LIMIT ITERACJI to ten sam mechanizm bezpieczeństwa co w /goal. Zawsze ustawiać.

## Git Worktree — równoległe warianty
- PROBLEM: sekwencyjne czekanie. Prosisz -> czekasz -> oglądasz -> prosisz o kolejną wersję -> ...
  Przy eksperymentach UI to marnowanie czasu. >> TRAFNA DIAGNOZA.
- ROZWIĄZANIE: git worktree = wiele kopii kodu na różnych branchach jednocześnie, każdy w osobnym
  folderze, bez klonowania repo.
- System autora (3 komendy + 1 subagent):
    /parallel-prep <nazwa-funkcji> <liczba worktree>     — tworzy N kopii
    (tu: skill UX/UI Guidelines generuje plan N wariantów do pliku .md)
    /parallel-execute <nazwa> <ścieżka-planu> <N>        — odpala N subagentów równolegle
    /parallel-cleanup <nazwa>                           — wybierasz zwycięzcę, reszta usunięta
  + subagent `parallel-developer`
- Demo: 5 wariantów landing page'a. Autor ocenia je 6/10, 7/10, 3/10, 5/10, 3/10 — wygrywa wariant
  "terminalowy". >> UCZCIWE: większość wariantów była słaba. Realny yield ~1 na 5.
- ⚠ OGRANICZENIE podane przez autora wprost: subagent = JEDNO okno kontekstowe.
  Zadanie wymagające kilku sesji (np. mikroserwis płatności) TEGO NIE PRZEJDZIE.
  >> DOBRZE, że to mówi. To jest właściwa granica stosowalności.
- Zastosowanie: eksperymenty UI/UX, proste warianty. NIE złożona logika.

## Remotion — generowanie wideo kodem
- Remotion = biblioteka do tworzenia wideo/animacji przez React. ZERO AI w samym renderze —
  to jest programowanie animacji, nie generatywne wideo. >> WAŻNE ROZRÓŻNIENIE, autor je robi.
- Instalacja: `npx create-video@latest` -> template blank -> Tailwind yes -> agent skills yes
  -> all detected agents -> scope project -> symlink. Potem `npm i`, `npm run dev`.
- DWA WORKFLOW:
  1. Z własnego pomysłu: prompt "pomysł użytkownika" -> pytania doprecyzowujące -> instrukcja -> kod.
  2. ODTWORZENIE ISTNIEJĄCEJ ANIMACJI: pobierz wideo (np. z Dribbble) -> wrzuć do Google AI Studio
     (Gemini) z promptem analizującym -> dostajesz instrukcję Remotion -> wklej do Claude Code.
     >> TO JEST SPRYTNE. Multimodalny model analizuje wideo, CC generuje kod. Wzorzec do zapamiętania.
- Czasy: prosta animacja telefonu ~7 min, odtworzenie sekcji pricing ~4 min, promo ~10 min.
- Jakość: "logo, kopii do poprawy", "mapka wygląda średnio". Pierwsza iteracja, do dopracowania.
- ⭐ PLIK "Remotion - Prompty" (8 KB) to NAJLEPSZY POJEDYNCZY ARTEFAKT W CAŁYM KURSIE.
  Dwa dopracowane prompty (analiza wideo / pomysł użytkownika), każdy w 5 warstwach:
  spec wizualna -> konfiguracja canvas -> zod schema + defaultProps -> choreografia animacji
  (per element: komponent, from, durationInFrames, spring/interpolate z liczbami) -> prompt do replikacji.
  Zawiera CHECKLISTY techniczne z konkretnymi antywzorcami, np.:
    - spring() ZAWSZE z fps z useVideoConfig() (pokazany poprawny i BŁĘDNY przykład)
    - interpolate() ZAWSZE z extrapolateLeft/Right: 'clamp'
    - zamiast Math.random() -> random('seed') z "remotion" (determinizm przy renderze!)
    - ZAKAZ CSS transitions/animations — powodują migotanie przy renderowaniu
    - Composition BEZ schema i defaultProps jest błędne
  >> To jest wzorzec inżynierii promptu, który warto skopiować NIEZALEŻNIE od Remotion.
     Struktura "warstwy + checklista antywzorców + wymuszenie weryfikacji na końcu" jest uniwersalna.

## Channels — sterowanie z telefonu (Telegram / Discord)
- Architektura: telefon -> komunikator -> bot (serwer MCP) -> AKTYWNA sesja CC na twoim komputerze -> z powrotem.
- ⚠ OGRANICZENIE FUNDAMENTALNE: sesja na komputerze MUSI żyć. Zamkniesz terminal, komputer
  pójdzie w standby, wyskoczy błąd — koniec. To nie jest praca w chmurze.
- Wymagania: Bun + subskrypcja + aktualny Claude (`claude update`) + aktualny marketplace Anthropic.
- TELEGRAM (prostszy): BotFather -> /newbot -> nazwa -> nazwa kończąca się na "bot" -> TOKEN.
  Potem: /plugin (scope user) -> /reload-plugins -> /telegram:configure <token> -> restart
  -> `claude --channels` -> pisz z telefonu -> kod autoryzacji.
  ⚠ Znany błąd "no such skill" -> zrestartować sesję (reload nie wystarcza).
- DISCORD (więcej kroków, więcej możliwości): Developer Portal -> New Application -> Bot -> Reset Token
  -> Message Content Intent = True -> OAuth2 -> uprawnienia: View Channels, Send Messages,
  Send Messages in Threads, Read Message History, Attach Files, Add Reactions -> URL -> dodaj do serwera.
  Potem /discord:configure <token>. Channel ID: prawy klik -> Copy channel ID.
  Domyślnie odpowiada tylko na @mention; flaga --no-mentions to wyłącza.
- RÓŻNICE: Discord ma fetch messages (do 100 wiadomości), pobieranie załączników, wątki, reakcje.
  Telegram prostszy w konfiguracji. Można uruchomić oba kanały naraz.
- BEZPIECZEŃSTWO: allow list (kto może pisać do bota), ochrona tokena.
- ⚠⚠ POWAŻNY PROBLEM, KTÓRY AUTOR ROZWIĄZUJE ŹLE:
  Praca z telefonu + pytania o uprawnienia na komputerze = zablokowana sesja.
  Autor rozwiązuje to flagą --dangerously-skip-permissions.
  >> To oznacza: agent z pełnymi uprawnieniami na twoim komputerze, sterowany przez komunikator,
     gdy ciebie nie ma przy klawiaturze. Przy tokenie bota w niepowołanych rękach albo przy
     prompt injection z treści, którą agent przetwarza — to jest realne ryzyko.
     ALLOW LIST TO ZA MAŁO. W dokumencie omówić bezpieczniejsze podejście (auto mode + allowlist narzędzi).
- Utrzymanie sesji: tmux (`tmux new -s <nazwa>`) na Linux/Mac. Windows — autor NIE MA rozwiązania.
- Autor sam deklaruje, że korzysta z narzędzia "Happy", nie z Channels.

## Lokalne modele przez Ollama
- Cel: zero kosztów inferencji. Autor od razu i uczciwie zastrzega, że to KOMPROMIS JAKOŚCIOWY.
- Wymagania sprzętowe: głównie VRAM karty graficznej.
- Komendy podane w lekcji:
    ollama list
    ollama pull <model>
    ollama launch claude --model <model>     >> ⚠ ZWERYFIKOWAĆ. Nietypowa składnia.
- Autor ma lokalnie: Mistral (3B) i Qwen (9B). "Większe na moim komputerze działają bardzo słabo".
- Skala: <4,5B "malutkie", 4,5-40B "małe", >40B wymaga poważnego sprzętu.
- Ranking: artificialanalysis.ai z filtrem open weights.
- WERDYKT AUTORA (uczciwy): małe modele OK do asystenta w Obsidianie/prostych zadań koncepcyjnych;
  do kodowania "jest to przepaść jeżeli chodzi o jakość". Model może się uruchomić, ale inferencja
  bywa tak wolna, że nie nadaje się do pracy; komputer się przegrzewa.
  >> ZGODA. Ta lekcja jest ciekawostką, nie ścieżką produkcyjną. Dobrze, że autor tego nie ubarwia.

## Landing page w React (lekcja praktyczna, wczesna)
- Pokazuje REALNY workflow: przygotowanie materiałów PRZED promptem.
  copy.md (cała treść + struktura sekcji), sample.html (plik referencyjny ze stylami z innego projektu),
  assety graficzne w folderze. >> TO JEST NAJWAŻNIEJSZA LEKCJA TEJ LEKCJI.
  Cytat autora: "to jest główna część pracy z Claude Code — musimy dużo czasu poświęcić na dobre
  przygotowanie plików, wymagań, środowiska, żeby potem sprawnie pracować". ZGODA W 100%.
- Voicing — dyktowanie promptu głosem. Autor używa regularnie do długich instrukcji.
- Odwołania do plików przez @ (małpka) — wskazanie konkretnych plików i folderów.
- Plan Mode -> pytania doprecyzowujące (tech stack, newsletter, video, animacje) -> drugi zestaw pytań
  -> agent planujący -> plan z fazami 1-7 + szacunkowy output w liniach kodu.
- ⭐ ZASADA OGRANICZONEGO ZAUFANIA: "zawsze przeglądamy plany przygotowane przez Claude,
  nie ufamy mu w stu procentach". >> POWTARZANE W KURSIE I SŁUSZNE.
- Po planie 3 opcje: auto accept / manual approve / type here (poprawki do planu).
  Autor: "w React nie mam dużego doświadczenia, więc większość takich zadań robię na autoakcepcie".
  >> UCZCIWE, ale to jest dokładnie ten wzorzec, który tworzy dług techniczny. Skomentować.
- Skille użyte: UX UI Guidelines, Tailwind Guidelines.
- ⭐ WZORZEC WART SKOPIOWANIA: w skillu UX UI Guidelines zapisane jest, że PO KAŻDEJ ZMIANIE WIZUALNEJ
  agent uruchamia MCP Playwright, otwiera stronę, robi screenshoty i sam ocenia wdrożenie.
  >> Zamknięta pętla sprzężenia zwrotnego dla frontendu. To jest realnie dobry pomysł.
- Poprawki przez ZRZUT EKRANU wklejony do terminala (Ctrl+V) + opis głosowy. Bardzo praktyczne.
- Deploy: MCP Netlify, jedna komenda. Potem podpięcie własnej domeny.
- Na końcu /init -> CLAUDE.md. Autor: "jestem zwolennikiem, żeby ten plik tworzyć samemu
  i dobrze spromptować, ale automatyczne generowanie nie jest najgorsze".
- Widoczny auto-compact w trakcie pracy (9% zapasu kontekstu) — realistyczny obraz pracy.
- Błędy po drodze: błędy TypeScript przy buildzie (poprawione automatycznie), zapętlenie przy
  instalacji zależności (3 próby), zły link do wideo w copy (błąd człowieka), modal nie do zamknięcia,
  grafika bez powiększenia. >> DOBRY, NIEWYIDEALIZOWANY OBRAZ.
