# Analiza materiałów szkoleniowych — Claude Code

Krytyczna analiza kursu **Akademia Claude Code** (Kacper Trzepieciński / AIBiz-Automatyzacje),
skonfrontowana z aktualną dokumentacją Anthropic.

## Co tu jest

| Ścieżka | Zawartość |
|---|---|
| `analiza/audyt-claude-code.html` | **Główny dokument.** Interaktywny — nawigacja, wyszukiwarka, filtry, checklisty z zapisem postępu, kopiowalne prompty. Otwórz w przeglądarce. |
| `analiza/notatki/modul1-podstawy.md` | Notatki z 9 transkrypcji modułu podstawowego |
| `analiza/notatki/modul2-sredniozaawansowany.md` | Notatki z 9 transkrypcji: skille, subagenci, MCP, hooki, workflows, /goal |
| `analiza/notatki/modul3-mobilne.md` | Notatki z modułu mobilnego (Nawykometr) |
| `analiza/notatki/modul4-dodatki.md` | Ralph Wiggum, git worktree, Remotion, Channels, Ollama |
| `analiza/notatki/konstruktor.md` | **Analiza makiety Konstruktora v2** — mocne strony, ryzyka, kolejność wdrożenia |
| `analiza/notatki/weryfikacja.md` | **Weryfikacja tez wobec dokumentacji** — potwierdzone / do korekty / ryzykowne |
| `analiza/notatki/transcript-map.json` | Mapa 39 transkrypcji → lekcje |
| `analiza/notatki/ALL_LINKS.json` | 126 linków wyekstrahowanych z PDF-ów |
| `analiza/notatki/allowed-domains.txt` | Lista domen do polityki sieciowej środowiska (44 pozycje) |

## Zakres analizy

- 5 PDF-ów (62 strony) — opisy lekcji, listy materiałów, timestampy
- **39 transkrypcji** lekcji (Google Docs, pobrane przez konektor Google Drive)
- **8 repozytoriów** sklonowanych i przeczytanych: 85 plików `SKILL.md`, 33 subagentów,
  22 hooki, 23 skrypty Dynamic Workflows
- Weryfikacja wobec `code.claude.com/docs` — stan na 17.09.2026

## Werdykt w jednym zdaniu

Kurs jest słaby jako encyklopedia (przeterminowane nazwy modeli, błędna nazwa zmiennej
środowiskowej, przeoczone pola konfiguracyjne), ale bardzo dobry jako szkoła metody —
pokazuje pełny cykl budowy aplikacji z agentem, z awariami włącznie.

Szczegóły, w tym 11 tez do korekty i plan wdrożenia, w dokumencie HTML.
