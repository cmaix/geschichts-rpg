# ARCHITEKTUR — Geschichts-RPG

> Textbasiertes Serious-RPG für das Geschichts-Abitur.
> Single-File-HTML · Vanilla JavaScript · Tailwind CSS (CDN) · keine Build-Tools, keine externen Abhängigkeiten.

---

## 1. Zielbild

Eine vollständig in einer Datei (`index.html`) lauffähige Text-Adventure-/RPG-Engine. Die Schülerin steuert eine **historische Rolle**, erkundet **Räume (locations)**, sammelt **Quellen-Artefakte** ins **Inventar** und nutzt dieses Quellenwissen, um in **NPC-Dialogen** abiturrelevante Entscheidungen freizuschalten. Pädagogischer Rahmen: **Dalton-Plan** (selbstgesteuert, selbstberichtigend).

Designprinzipien:

- **Datengetrieben.** Die gesamte Spielwelt liegt als deklaratives `WORLDS`-Objekt vor. Die Engine *parst* diese Daten — sie enthält keine fest verdrahteten Inhalte. Neue Welten/Räume = nur Daten ergänzen, kein Engine-Code.
- **Eine Wahrheit für den Zustand.** Ein einziges `state`-Objekt hält den gesamten Spielstand. Die UI ist eine reine Funktion des Zustands (`render(state)`).
- **Progressives Freischalten.** Dialogoptionen werden über `inventory.includes(requiresItem)` gated — Quellenarbeit ist Spielmechanik, nicht Dekoration.
- **Kein Framework, kein Bundler.** Alles inline; per Doppelklick im Browser lauffähig.

---

## 2. Tech-Stack & Dateistruktur

| Schicht        | Technologie                                  |
|----------------|----------------------------------------------|
| Markup         | HTML5, eine Datei                            |
| Styling        | Tailwind CSS via CDN + minimales `<style>`   |
| Logik          | Vanilla JS (ES2020), inline `<script>`       |
| Persistenz     | optional `localStorage` (Phase „Polish")     |
| Schrift/Look   | Mono-/Terminal-Font, Dark-Slate + Amber      |

```
geschichts-rpg/
├── index.html        # Engine + Daten + UI (Single File)
├── ARCHITEKTUR.md    # dieses Dokument
├── PROTOKOLL.md      # Spiel-/Projektprotokoll (GDD + Changelog)
└── TODO.md           # abarbeitbarer Bauplan
```

---

## 3. UI-Layout (3-Spalten, RPG-Look)

Terminal-Retro, Dark-Slate mit Amber-Akzenten. Ab `lg` dreispaltig, darunter gestapelt.

```
┌──────────────────────────────────────────────────────────────────┐
│  KOPF: Welt-Titel · Rolle · (Welt verlassen)                       │
├───────────────┬────────────────────────────┬───────────────────────┤
│  LINKS         │  MITTE                     │  RECHTS               │
│  Charakter     │  Text-Log (Story)          │  Navigation & Aktion  │
│                │                            │                       │
│ • Name / Rolle │ » Raumbeschreibung         │ [Räume wechseln]      │
│ • 🎯 Einfluss   │ » Dialogzeilen            │   → Saal              │
│   ▓▓▓▓▓░░░ 40  │ » Konsequenz-Feedback      │   → Ballhaus          │
│ • 👁 Verdacht   │ (append-only, scrollt)     │ [Aktionen]            │
│   ▓░░░░░░ 10   │                            │  🔍 Quelle untersuchen │
│ • 📜 Inventar   │                            │  💬 Person ansprechen  │
│   - Sieyès …   │                            │                       │
└───────────────┴────────────────────────────┴───────────────────────┘
```

- **Links — Charakter-Panel:** Name, Rolle, Statusbalken (`influence`, `suspicion`), Inventarliste der gesammelten Artefakte (klickbar → zeigt Quellentext im Log).
- **Mitte — Text-Log:** append-only Story-Strom (Raumbeschreibung, Dialoge, Feedback). Jüngster Eintrag sichtbar (auto-scroll).
- **Rechts — Aktionspanel:** dynamische Buttons aus den Daten des aktuellen Raums: Ausgänge (`exits`), Artefakte aufheben, NPCs ansprechen.

---

## 4. Zustandsmodell (`state`)

```js
const state = {
  worldId: null,            // aktive Welt, z.B. "welt_1789"
  character: {
    name: "",               // gewählte Rollenbezeichnung
    role: "",               // role_name aus den Daten
    influence: 50,          // 🎯 politisches Gewicht   (0..100)
    suspicion: 10           // 👁 Misstrauen/Gefahr      (0..100)
  },
  inventory: [],            // Array von Artefakt-IDs, z.B. ["quelle_sieyes"]
  currentLocation: "start", // Schlüssel in world.locations
  unlockedKnowledge: [],    // freigeschaltete Lerninhalte/Dialogachsen
  log: [],                  // Text-Log-Einträge {type, text}
  npcState: {}              // pro NPC: bereits getroffene Entscheidungen
};
```

**Stat-Regeln**

| Wert        | Bereich | Bedeutung                              | Kritisch                |
|-------------|---------|----------------------------------------|-------------------------|
| `influence` | 0–100   | politische Durchsetzungskraft          | < 20 → Bedeutungsverlust|
| `suspicion` | 0–100   | Misstrauen der Obrigkeit               | > 70 → Verhaftungsgefahr|

Werte werden bei jeder Änderung auf `[0,100]` geklemmt (`clamp`). Kritische Schwellen färben den Balken signalrot und können ein Spielende/Warnereignis auslösen.

---

## 5. Datenmodell (deklarative Welt)

```
WORLDS  (Array)
└─ World
   ├─ world_id, title
   ├─ roles[]              → { role_name, desc, start_stats:{influence,suspicion} }
   ├─ startLocation        → Schlüssel der Anfangslocation
   ├─ locations { key: Location }
   │  └─ Location
   │     ├─ name, description
   │     ├─ artifacts[]    → { id, name, text, requiresInspection }
   │     ├─ npcs[]
   │     │  └─ NPC
   │     │     ├─ name, dialogStart
   │     │     └─ choices[]
   │     │        └─ Choice
   │     │           ├─ text
   │     │           ├─ requiresItem  (Artefakt-ID | null)   ← Gate
   │     │           ├─ effects { influence, suspicion }
   │     │           ├─ nextLocation  (optional)
   │     │           └─ feedback
   │     └─ exits[]        → Liste navigierbarer Location-Keys
   └─ abiEvaluation        → { summary, daltonTask }
```

> **Hinweis zur Datenpflege:** Im Original-Spec stehen Effekte als `+20` (führendes Plus) — in JS ungültig. In `index.html` werden sie als reine Zahlen (`20`, `-15`) geschrieben.

---

## 6. Engine — Funktionsüberblick (Game Loop)

```
            ┌───────────────────────────────────────────────┐
            │  init() → renderMenu()                         │
            │     │  Rollen-/Weltauswahl                     │
            │     ▼                                          │
            │  startWorld(worldId, roleIndex)                │
            │     │  state initialisieren                    │
            │     ▼                                          │
   ┌────────►  enterLocation(key) ──► logRoom()              │
   │        │     │                                          │
   │        │     ├─ renderActions()  (exits, artefakte, npc)│
   │        │     └─ render()         (alle 3 Spalten)       │
   │        │                                                │
   │  Aktion des Spielers:                                   │
   │   ├─ pickUpArtifact(id)  → inventory.push → render()    │
   │   ├─ inspectArtifact(id) → logSource()                  │
   │   ├─ talkToNPC(idx)      → logDialog() → renderChoices()│
   │   └─ chooseDialog(npc,i) → applyEffects()               │
   │                            checkCrisis()                │
   │                            logFeedback()                │
   │                            unlockKnowledge()            │
   │                            └─ next? enterLocation()─────┘
   │
   └─ Wenn Quest-/Weltende: showEvaluation() (Abi + Dalton)
```

**Kernfunktionen**

| Funktion                       | Aufgabe                                                        |
|--------------------------------|---------------------------------------------------------------|
| `renderMenu()`                 | Welt- und Rollenauswahl rendern                               |
| `startWorld(id, roleIdx)`      | `state` setzen, Startstats aus Rolle, Startlocation laden     |
| `enterLocation(key)`           | Raum betreten, Beschreibung ins Log, Aktionen neu aufbauen    |
| `render()`                     | Komplettes Re-Render aller drei Spalten aus `state`           |
| `renderSidebar()`              | Charakter, Statusbalken, Inventar                             |
| `renderLog()`                  | append-only Text-Log, auto-scroll                             |
| `renderActions()`              | Buttons: Ausgänge, aufhebbare Artefakte, ansprechbare NPCs    |
| `pickUpArtifact(id)`           | Artefakt ins Inventar, UI aktualisieren                       |
| `inspectArtifact(id)`          | Quellentext im Log anzeigen (Analysehilfe)                    |
| `talkToNPC(idx)`               | NPC-Eröffnung loggen, passende Choices rendern                |
| `isChoiceUnlocked(choice)`     | `!choice.requiresItem \|\| inventory.includes(...)`           |
| `chooseDialog(npcIdx, cIdx)`   | Effekte anwenden, Feedback, ggf. `nextLocation`, Wissen freischalten |
| `applyEffects(effects)`        | Stats verändern + clampen                                     |
| `checkCrisis()`                | Schwellen prüfen, rote Färbung / Warnung                      |
| `showEvaluation()`             | Abi-Zusammenfassung + Dalton-Aufgabe (+ kopierbar)            |
| `goHome()`                     | Zustand zurücksetzen, ins Menü                                |

---

## 7. Rendering-Strategie

- **Sidebar & Aktionen:** vollständiges Neuzeichnen aus `state` (idempotent, einfach).
- **Text-Log:** *append-only*. Neue Einträge werden angehängt; der Container scrollt automatisch ans Ende. Eintragstypen (`room`, `dialog`, `choice`, `feedback`, `source`, `system`) erhalten je eine eigene Farb-/Stil-Klasse.
- **Gating sichtbar machen:** gesperrte Choices werden ausgegraut mit Hinweis „🔒 benötigt: <Artefaktname>", statt sie zu verstecken — so wird die Quellenmechanik didaktisch transparent.

---

## 8. Conditional Unlocking (didaktischer Kern)

```js
function isChoiceUnlocked(choice) {
  return !choice.requiresItem || state.inventory.includes(choice.requiresItem);
}
```

Eine quellenbasierte (abiturrelevante) Option ist nur wählbar, wenn das passende Artefakt vorher untersucht/aufgehoben wurde. Das zwingt die Lernenden zur Quellenarbeit, bevor sie historisch fundiert handeln.

---

## 9. Erweiterbarkeit

- **Neue Welt:** Eintrag in `WORLDS` ergänzen — Engine erkennt sie automatisch im Menü.
- **Neuer Raum:** `locations`-Eintrag + Verlinkung über `exits`/`nextLocation`.
- **Neues Artefakt/NPC/Choice:** rein deklarativ; keine Engine-Änderung nötig.
- **Persistenz (optional):** `state` als JSON in `localStorage` serialisieren (`save`/`load`).

---

## 10. Nicht-Ziele (bewusst ausgeklammert)

- Kein Backend, keine Accounts, kein Netzwerk.
- Keine externen Bilder (nur Emojis/ASCII).
- Keine Animations-Engine — nur sanfte CSS-Transitions.
