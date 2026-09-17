# WERYFIKACJA TEZ KURSU WOBEC AKTUALNEJ DOKUMENTACJI (stan: 17.09.2026)
Źródła: code.claude.com/docs (memory, sub-agents, cloud-environments, goal, workflows, hooks),
tabela modeli ze skilla claude-api, sklonowane repozytoria kursu.

## ✅ POTWIERDZONE — teza kursu zgodna z dokumentacją

| Teza kursu | Status |
|---|---|
| CLAUDE.md: celuj poniżej 200 linii | ✅ "target under 200 lines per CLAUDE.md file. Longer files consume more context and reduce adherence" |
| CLAUDE.md może leżeć w katalogu głównym LUB w .claude/ | ✅ "`./CLAUDE.md` or `./.claude/CLAUDE.md`" |
| Trzy warstwy CLAUDE.md (user / projekt / local) | ✅ (dokumentacja dodaje czwartą: managed policy dla organizacji — nieistotna dla indywidualnych) |
| .claude/rules/ istnieje i wspiera warunkowe wczytywanie | ✅ |
| CLAUDE_CODE_NEW_INIT=1 dla interaktywnego /init | ✅ nadal aktualne, nadal wymaga zmiennej |
| auto memory istnieje, MEMORY.md, limit 200 linii / 25 KB | ✅ dokładnie tak |
| Okno kontekstowe 1M | ✅ dla Opus/Sonnet/Fable — ALE patrz korekta o Haiku niżej |
| Dynamic Workflows: zapis do .claude/workflows/ | ✅ |
| /goal: pętla pracownik + kontroler, warunek mierzalny | ✅ "a small fast model checks whether the condition holds" |
| /goal: jeden cel na sesję | ✅ |
| Hooki: event + matcher + command + exit code | ✅ |
| Subagent ma własne okno kontekstowe, wraca raport | ✅ |
| Agent Teams są domyślnie wyłączone i eksperymentalne | ✅ |

## ⚠️ DO KOREKTY — teza nieprecyzyjna lub nieaktualna

### 1. NAZWY MODELI — najpoważniejsze przeterminowanie
Kurs mówi (niespójnie, w różnych lekcjach): "Opus 4.6", "Opus 4.7", "Sonnet 4.6", "Haiku 4.5", "Haiku 4.0".
STAN FAKTYCZNY: aktualna generacja to rodzina Claude 5.
  Claude Fable 5.1 (claude-fable-5-1)  — 1M kontekstu, najbardziej zdolny szeroko dostępny
  Claude Opus 5     (claude-opus-5)     — 1M
  Claude Sonnet 5   (claude-sonnet-5)   — 1M
  Claude Haiku 4.5  (claude-haiku-4-5)  — 200K  ⬅ UWAGA
Modele 4.6/4.7/4.8 nadal działają, ale to poprzednie generacje.
WNIOSEK: nie ucz się nazw modeli z tego (ani żadnego) kursu. Sprawdzaj /model w narzędziu.
Sam autor używa w jednej z późniejszych lekcji modelu "Fable" — czyli materiał był nagrywany
przez kilka generacji modeli i nazwy dryfują w obrębie samego kursu.

### 2. ⭐ HAIKU MA 200K, NIE 1M — kurs tego nie mówi, a to ma konsekwencje
Kurs w dwóch miejscach radzi Haiku: (a) jako tańszy model dla subagentów, (b) jako kontroler w /goal.
Rada jest dobra, ale kurs NIGDZIE nie mówi, że Haiku 4.5 ma **200K** okna, a nie 1M jak reszta.
KONSEKWENCJA PRAKTYCZNA: subagent przestawiony na Haiku dla oszczędności ma 5× mniejsze okno.
Przy zadaniu typu "przejrzyj 50 plików" wysypie się tam, gdzie Opus by przeszedł.
To jest realna pułapka kosztowa-jakościowa, której kurs nie zna.

### 3. ⭐ SUBAGENT A SKILLE — kurs upraszcza i przez to myli
Kurs: "subagent NIE MA DOSTĘPU do skilli, musisz je wymienić we frontmatterze w kluczu skills".
Dokumentacja: klucz `skills` PRELOADUJE treść skilla do kontekstu subagenta na starcie.
  "This field controls which skills are PRELOADED, not which skills the subagent can ACCESS:
   without it, the subagent can still discover and invoke project, user, and plugin skills
   through the Skill tool during execution."
CZYLI: bez `skills` subagent nadal MOŻE sięgnąć po skill sam. Klucz to optymalizacja
(gwarancja + oszczędność rundy na odkrywanie), nie bramka dostępu.
Rada kursu pozostaje DOBRĄ PRAKTYKĄ, ale uzasadnienie jest błędne.

### 4. ⭐⭐ FRONTMATTER SUBAGENTA — kurs zna 5 pól, jest ich 18
Kurs wymienia: name, description, model, tools, skills.
Dokumentacja wymienia dodatkowo m.in.:
  disallowedTools, permissionMode, maxTurns, mcpServers, hooks, memory, background,
  omitClaudeMd, effort, isolation, color, initialPrompt, experimental
NAJWAŻNIEJSZE PRZEOCZENIA:
  • `maxTurns`      — twardy limit tur subagenta. To jest pas bezpieczeństwa, którego kurs
                      szuka ręcznie w /goal i w Ralphie ("zatrzymaj się po 20 turach").
  • `memory`        — subagent może mieć WŁASNĄ trwałą pamięć (user/project/local).
  • `effort`        — per-subagent, nie tylko globalnie.
  • `isolation: worktree`  ⬅ TO JEST DUŻE
  • `permissionMode` — w tym `auto`.

### 5. ⭐⭐ GIT WORKTREE JEST JUŻ WBUDOWANY — cała lekcja kursu jest obejściem
Kurs poświęca osobną lekcję na własny system 3 komend (/parallel-prep, /parallel-execute,
/parallel-cleanup) + subagent parallel-developer, żeby uruchamiać warianty w git worktree.
Dokumentacja: subagent ma pole `isolation: worktree` — "Set to `worktree` for isolated git worktree".
WNIOSEK: to, co kurs buduje ręcznie, jest dziś natywną funkcją jednego pola we frontmatterze.
Lekcja nadal ma wartość koncepcyjną (PO CO to robić), ale implementacja jest przestarzała.

### 6. AGENT TEAMS — ZŁA NAZWA ZMIENNEJ ŚRODOWISKOWEJ
Kurs podaje trzy różne warianty (transkrypcja vs PDF):
  CLAUDE_CODE_EXPERIMENTAL = 1
  claude.code.experimental.agents
  CLAUDE_CODE_EXPERIMENTAL_AGENTS
Dokumentacja: **CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1**
Konsekwencja: kto przepisze z kursu, funkcji nie włączy i nie będzie wiedział dlaczego.

### 7. AUTO MEMORY — kurs mówi, że nie masz nad tym kontroli. Masz.
Kurs: "my nie mamy wpływu na to, jakie informacje tam się zapisują (...) Ty nie widzisz tych plików".
Dokumentacja: pliki są zwykłym markdownem, można je czytać, edytować i kasować;
  /memory otwiera folder; autoMemoryEnabled wyłącza funkcję (globalnie lub per projekt);
  CLAUDE_CODE_DISABLE_AUTO_MEMORY=1; autoMemoryDirectory zmienia lokalizację.
Lokalizacja: ~/.claude/projects/<projekt>/memory/ — dzielona przez wszystkie worktree tego repo, lokalna dla maszyny.

### 8. AUTO DREAM — NIEPOTWIERDZONE
Kurs opisuje "auto dream" jako mechanizm porządkujący pliki auto memory,
sam zaznaczając: "jeszcze nie działa oficjalnie tak jak deklarują, są to pewne wycieki".
Weryfikacja: w dokumentacji pamięci NIE MA takiego pojęcia.
WNIOSEK: traktować jako niepotwierdzoną ciekawostkę, nie budować na tym procesu.
Na plus dla autora, że sam to oznaczył jako wyciek.

### 9. PRECEDENCJA CLAUDE.md — kurs upraszcza
Kurs sugeruje, że pliki się "nadpisują" wg hierarchii.
Dokumentacja: pliki są KONKATENOWANE, nie nadpisywane. Kolejność od katalogu głównego
w dół do katalogu roboczego; CLAUDE.local.md doklejany PO CLAUDE.md na danym poziomie.
Instrukcje bliżej miejsca uruchomienia są czytane jako ostatnie.
Sprzeczne reguły = "Claude may pick one arbitrarily" — więc porządkowanie to twoja robota.

### 10. FRONTMATTER REGUŁ: `paths`, nie `path`
Kurs mówi o "frontmatterze z argumentem path".
Dokumentacja i sam kod w repo kursu: pole nazywa się **`paths`** i przyjmuje LISTĘ globów:
  ---
  paths:
    - "src/api/**/*.ts"
  ---
Drobiazg, ale literówka = reguła się nie wczyta.

### 11. LICZBY, KTÓRE SIĘ ZDEZAKTUALIZUJĄ
"133 pluginy", "ponad 140 skilli", "91 tysięcy skilli na skills.sh", "prawie milion na skillsmp".
To zdjęcia z konkretnego dnia. Nie cytować jako fakty.
Ceny planów (Pro 17 / Max x5 100 / Max x20 200 USD) — sprawdzać na stronie Anthropic, nie w kursie.

## 🔴 TEZA, KTÓRĄ TRZEBA SKOMENTOWAĆ MOCNIEJ NIŻ ROBI TO KURS

### HOOK NIE JEST GRANICĄ BEZPIECZEŃSTWA
Kurs pokazuje hook blokujący .env i mówi mimochodem: "Claude szuka oczywiście obejścia.
Natomiast już nas to nie interesuje."
Dokumentacja potwierdza właściwy model myślenia:
  "Claude treats them [CLAUDE.md i auto memory] as CONTEXT, NOT ENFORCED CONFIGURATION.
   To BLOCK an action regardless of what Claude decides, use a PreToolUse hook instead."
  oraz: "Settings rules are enforced by the client regardless of what Claude decides to do.
   CLAUDE.md instructions shape Claude's behavior but are not a hard enforcement layer."
CZYLI: hierarchia siły to
  CLAUDE.md / rules  = sugestia (Claude może zignorować)
  hook PreToolUse    = egzekwowane przez klienta
  permissions.deny w settings = egzekwowane przez klienta
Kurs zna hooki, ale nie stawia jasno tej hierarchii ani nie wspomina o permissions.deny
jako właściwym narzędziu do twardej blokady.

### --dangerously-skip-permissions JAKO DOMYŚLNY TRYB PRACY
Kurs rekomenduje tryb YOLO w co najmniej czterech miejscach:
  lekcja o uprawnieniach ("ja sam często tak pracuję"),
  lekcja o Gicie (pracuje w Bypass),
  /goal ("automode ALBO bypass permissions"),
  Channels (bypass, żeby sterować z telefonu bez pytań na komputerze)
Istnieje bezpieczniejsza alternatywa — AUTO MODE — którą kurs wymienia tylko raz, mimochodem,
jako równorzędną z bypassem. Anthropic opisuje auto mode jako "a safer way to skip permissions".
REKOMENDACJA DLA DOKUMENTU: rozdzielić te dwie rzeczy i wskazać auto mode jako domyślny wybór,
a bypass jako wyjątek dla izolowanego, jednorazowego środowiska.
Zwłaszcza w scenariuszu Channels (agent z pełnymi prawami sterowany z komunikatora,
gdy nie ma cię przy komputerze) rekomendacja kursu jest realnie ryzykowna.

## ŚWIEŻOŚĆ MATERIAŁU — na plus dla kursu
Repozytoria kursu są ŻYWE: ostatnie commity 01.09.2026 i 06.09.2026 (11-16 dni temu).
learned-patterns.md zawiera wpisy datowane 22-23.06.2026.
Kurs pokrywa funkcje, które są świeże: Dynamic Workflows, /goal, Agent Teams, auto memory,
Channels. To NIE jest materiał odgrzewany. Problem dotyczy szczegółów (nazwy modeli, nazwy
zmiennych, pola frontmattera), nie koncepcji.
