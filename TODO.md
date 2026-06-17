# TODO — Geschichts-RPG

Abarbeitbarer Bauplan für die Engine. Reihenfolge ist sinnvoll von oben nach unten; jede Phase ist
unabhängig testbar. Querverweise: [ARCHITEKTUR.md](ARCHITEKTUR.md) · [PROTOKOLL.md](PROTOKOLL.md)

Legende: `[ ]` offen · `[~]` in Arbeit · `[x]` erledigt

---

## Phase 0 — Setup
- [x] Projektordner `geschichts-rpg/` anlegen
- [x] ARCHITEKTUR.md, PROTOKOLL.md, TODO.md schreiben
- [x] `index.html`-Grundgerüst (Tailwind-CDN, Terminal-Font, Dark-Slate-Theme)
- [x] Eintrag in `.claude/launch.json` (Port 4139 + `--directory`) für Browser-Preview

## Phase 1 — Datenmodell
- [x] `MODULES`-Array mit Welt 1789 angelegt (Rollen, Locations, Artefakte, NPCs, Choices, Auswertung)
- [x] Effekte als reine Zahlen geschrieben (Spec-`+20` → `20`) — siehe ARCHITEKTUR §5
- [x] `statLabels` definiert (`influence` / `suspicion`); Krisenschwellen in `statBlock()`
- [x] `startLocation` je Modul festgelegt (`versailles_saal`)

## Phase 2 — Zustand & Engine-Kern
- [x] `state`-Objekt initialisiert (character, inventory, currentLocation, log, dialogNpcIndex, gameOver)
- [x] `clamp()` + `applyEffects()` (Stats verändern, auf 0–100 begrenzen)
- [x] `selectRole(roleId)` — Startstats aus Rolle übernehmen
- [x] `changeLocation(key)` — Raum betreten, Beschreibung ins Log, Aktionen neu bauen
- [x] `goHome()` — Reset in den Menü-Zustand

## Phase 3 — Rendering (3-Spalten-UI)
- [x] `renderMenu()` — Welt- und Rollenauswahl (große Kacheln)
- [x] `renderSidebar()` — Charakter, Statusbalken, Warnsystem, Inventarliste (links)
- [x] `renderLog()` — append-only Text-Log mit Auto-Scroll, Eintragstypen farbig (Mitte)
- [x] `renderActions()` — Ausgänge, aufhebbare Artefakte, ansprechbare NPCs (rechts)
- [x] Responsives Layout: `md` dreispaltig (col-span 3/6/3), darunter gestapelt

## Phase 4 — Spielmechanik
- [x] `pickUpItem(id)` — Artefakt ins Inventar, Button entfernt, Quellentext geloggt
- [x] `inspectItem(id)` — Quellentext erneut im Log anzeigen (Inventar-Klick)
- [x] `interactWithNPC(idx)` — Eröffnungszeile loggen, Choices rendern
- [x] Gating via `inventory.includes(requires_item)` (in `renderActions` + `chooseDialog` gesichert)
- [x] Gesperrte Choices ausgegraut + Hinweis „🔒 Benötigt: <Artefakt>"
- [x] `chooseDialog(npcIdx, choiceIdx)` — Effekte, Feedback, optional `next_location`
- [x] `checkGameOver()` + Krisenfärbung/Blinken in `statBlock()`/`renderSidebar()`

## Phase 5 — Auswertung & Dalton
- [x] `showEvaluation()` — Abi-Zusammenfassung + Dalton-Aufgabe der Welt
- [x] Kopierbarer Mappen-Eintrag (Textarea + „In Zwischenablage kopieren")
- [x] Navigations-Buttons: erneut spielen · andere Rolle / Hauptmenü

## Phase 6 — Politur
- [x] Sanfte CSS-Transitions für Statusbalken + Log-/Fade-Animationen
- [x] Leerzustände/Hinweise (leeres Inventar, keine Aktionen)
- [x] `localStorage` Speichern bei jedem Zustandswechsel + „Fortsetzen"-Karte im Menü (v2.0.0)
- [x] Fokus-Ringe auf interaktiven Elementen

## Phase 7 — QA / Verifikation (Browser-Preview) ✅ bestanden
- [x] Beide Rollen starten, korrekte Startstats (Jean-Luc 40/10, Comte 60/0)
- [x] Artefakt aufheben → erscheint im Inventar
- [x] Quellen-Gating: Option erst nach Artefakt wählbar (🔒 → „Quellengestützt")
- [x] Alle Choices: Effekte + Feedback + Raumwechsel korrekt
- [x] Rollenspezifische NPCs (Jean-Luc→Mirabeau/Herold, Comte→Artois/König)
- [x] Game Over bei Verdacht ≥ 80 (Comte-Härte-Pfad → 100%, Game-Over-Screen)
- [x] Auswertung erscheint, Kopier-Button funktioniert
- [x] Keine Konsolenfehler; Layout responsiv (Desktop 1280 + mobil)
- [x] Abgleich gegen „Definition of Done" in PROTOKOLL §9
- [x] **Vollabnahme v2.2.0:** alle 8 Rollen (11 Pfad-Varianten) automatisiert bis zur Auswertung durchgespielt — 0 Fehler (Ergebnistabelle in PROTOKOLL §10)
- [x] Game-Over-Codepfad separat zertifiziert (feuert bei Verdacht ≥ 80)

## Phase 8 — Inhaltsausbau
- [x] Comte-de-Vance-Pfad mit eigenen Dialogen (v1.0)
- [x] Welt 2: Frankfurt 1848/49 (Paulskirche) — Rollen Eduard (liberal) & Gustav (radikal) (v2.0.0)
- [x] Welt 3: Berlin 1933 (Krolloper, Otto Wels) (v2.0.0)
- [x] Dalton-Mappen-Zertifikat als `.txt`-Download (Blob) (v2.0.0)
- [x] Welt 5: Leipzig 1989 (Montagsdemos) — Rollen Sabine (Bürgerrechtlerin) & Oberstleutnant Wagner (Stasi) (v2.1.0)
- [x] Zweite spielbare Rolle für Welt 1933: Heinrich (Zentrumspartei) — historisches „Ja" vs. kontrafaktisches „Nein" (v2.2.0)

> **Inhaltlicher Hinweis (v2.0.0):** Die mitgelieferten Daten enthielten für die
> Radikalen-Rolle *Gustav* (Welt 1849) keine Dialoge. Diese Branche (NPC „Sprecher der
> Linken / Fraktion Donnersberg", Ausgang Reichsverfassungskampagne → Rastatt 1849) wurde
> ergänzt und der Verdachtswert so austariert (+35), dass Gustav die Reflexion erreicht,
> statt sofort im Game-Over zu enden. Bei Bedarf kann der Lehrer-Text angepasst werden.

---

## Phase 9 — Backlog (benötigt Lehrer-Input / Designentscheidung)

> Die Phasen 0–8 sind vollständig abgeschlossen und auf GitHub veröffentlicht (v2.2.0). Die folgenden
> Punkte sind **echte, noch offene Erweiterungen** — sie brauchen aber entweder fachliche Quellen-Vorgaben
> von dir oder eine bewusste Designentscheidung, daher bleiben sie bewusst offen.

- [x] **Balancing Game-Over (v2.3.0):** Gustav (1849) erhält eine bewusst „tödliche" 3. Option
      (sofortiger Putsch → Verdacht 40→85 → Game-Over/Rastatt-Erschießung). Seine Reichsverfassungs-
      kampagne-Option erreicht weiterhin die Reflexion. Mechanik damit im regulären Spiel erlebbar.
- [x] **Zusätzliche Quelle für Leipzig 1989 (v2.3.0):** 3. Raum „Bornholmer Straße, 9. Nov." mit
      Schabowski-Zettel; beide historischen Leipzig-Pfade münden dort in die Mauerfall-Entscheidung.
- [x] **Druck-Stylesheet (v2.3.0):** `@media print` + 🖨️-Button drucken das Dalton-Zertifikat
      sauber direkt aus dem Browser (zusätzlich zum `.txt`-Export).
- [ ] **Mehrräumige Welten** für 1933 (aktuell 1 Raum) — z. B. Vorraum/Straße als Atmosphäre (1989 jetzt 3 Räume).
- [ ] **Welt 4: 1870/71 Reichsgründung** oder **Kaiserreich/Imperialismus** als Lückenfüller (optional).

## Offene Entscheidungen — entschieden in v1.0
- [x] Verdachts-Spielende: **harter Game-Over bei `≥ 80`** (Warnung + Blinken ab `≥ 50`) — wie im Spec
- [x] NPC-Entscheidungen **wiederholbar** (kein `npcState`-Lock; Räume frei wieder betretbar)
- [x] Persistenz: v1.0 sitzungsbasiert → **v2.0.0: `localStorage`-Autosave + Fortsetzen-Button**
- [x] **Comte-Pfad in v1.0 enthalten** (eigener NPC Artois im Saal, König Ludwig im Ballhaus)
