# PROTOKOLL — Geschichts-RPG

Sauberes Projekt- und Spielprotokoll (Game Design Document + Änderungslog) für das textbasierte
Serious-RPG zum Geschichts-Abitur.

- **Stand:** 2026-06-17
- **Status:** ✅ **v2.1.0** — 4 Welten (1789 / 1848-49 / 1933 / 1989), `localStorage`-Persistenz & Zertifikat-Download, im Browser verifiziert · siehe [TODO.md](TODO.md)
- **Verwandte Dokumente:** [ARCHITEKTUR.md](ARCHITEKTUR.md), [TODO.md](TODO.md)

---

## 1. Projektkontext

Das RPG ist der dritte Baustein einer Suite von Geschichts-Lernspielen (alle Single-File-HTML, Dalton-Plan):

| Projekt              | Konzept                                              | Status   |
|----------------------|------------------------------------------------------|----------|
| `geschichts-simulator` | Szenario-Durchlauf, 6 Module, Statusbalken         | ✅ fertig |
| `quellen-matrix`       | Quellen-Entscheidungs-Matrix (historisch/kontrafaktisch) | ✅ fertig |
| **`geschichts-rpg`**   | **Charakter-RPG mit Inventar, Räumen, NPC-Dialogen** | ✅ Welt 1 fertig |

Gemeinsame Designsprache: Dark-Slate, Amber-Akzente, keine externen Bilder, keine Build-Tools.

---

## 2. Spielidee & Pädagogik

**Historisches Charakter-Rollenspiel.** Die Lernenden übernehmen eine historische Rolle, bewegen sich durch
Räume, sammeln **Quellen-Artefakte** und setzen das Quellenwissen ein, um in Dialogen mit historischen NPCs
folgenschwere Entscheidungen zu treffen.

Didaktischer Kern (Dalton-Plan):

- **Selbstgesteuert:** freie Raum- und Entscheidungswahl.
- **Selbstberichtigend:** jede Entscheidung erzeugt sofort historisches Feedback.
- **Quellengebunden:** abiturrelevante Optionen sind erst nach Quellenarbeit verfügbar (Gating).
- **Reflexiv:** am Questende Abi-Zusammenfassung + Dalton-Reflexionsaufgabe.

**Lernziele:** Quellen analysieren und einordnen, Handlungsspielräume historischer Akteure beurteilen,
Ursache-Wirkungs-Ketten der Französischen Revolution (Phase 1789) nachvollziehen.

---

## 3. Spielablauf (Player Journey)

```
Hauptmenü
  → Welt wählen (Welt 1: Paris & Versailles 1789)
  → Rolle wählen (Jean-Luc, Dritter Stand  /  Comte de Vance, Adliger)
  → Startraum: Sitzungssaal der Generalstände
      • Raumbeschreibung lesen
      • Artefakt untersuchen/aufheben  → Inventar
      • NPC ansprechen (Mirabeau)      → Dialog
      • quellenbasierte Option wählen  → Effekte + Feedback
      • Übergang → Ballhaus
  → Ballhaus
      • Ballhausschwur-Entwurf aufheben
      • Herold (Dreux-Brézé) konfrontieren
  → Quest-/Weltende
      → Auswertung: Abi-Zusammenfassung + Dalton-Aufgabe (kopierbar)
```

---

## 4. Statuswerte

| Wert         | Start (Jean-Luc) | Start (Comte) | Bedeutung                         | Krisenschwelle        |
|--------------|------------------|---------------|-----------------------------------|-----------------------|
| 🎯 Einfluss   | 40               | 60            | politische Durchsetzungskraft     | < 20 → Bedeutungsverlust |
| 👁 Verdacht   | 10               | 0             | Misstrauen der königlichen Macht  | > 70 → Verhaftungsgefahr |

Beide Werte sind auf `0–100` begrenzt. Kritische Schwellen werden signalrot dargestellt.

---

## 5. Welt 1: Paris & Versailles 1789 — Inhaltsprotokoll

### 5.1 Rollen

| Rolle | Beschreibung | Start (Einfluss/Verdacht) |
|-------|--------------|----------------------------|
| **Jean-Luc** — Abgeordneter des Dritten Standes | Bürgerlicher Anwalt aus Bordeaux, aufklärungsgeschult. Ziel: Freiheit & Verfassung. | 40 / 10 |
| **Comte de Vance** — Konservativer Adliger | Treuer Gefolgsmann des Königs. Ziel: Erhalt der gottgegebenen Ordnung & Privilegien. | 60 / 0 |

### 5.2 Raum A — 📍 Sitzungssaal der Generalstände

> „Die Luft ist stickig. Die Stände sind getrennt. Die Adligen weigern sich, nach Köpfen abzustimmen.
> Auf dem Tisch der Schreiber liegt ein Dokument."

**Artefakt:** `quelle_sieyes` — 📜 Flugblatt von Abbé Sieyès (Januar 1789)
> »Was ist der Dritte Stand? Alles. Was ist er bisher in der politischen Ordnung gewesen? Nichts.
> Was fordert er? Etwas zu werden.«
> *Wirkung:* schaltet die Dialogoption „Souveränität des Volkes" frei.

**NPC:** Graf von Mirabeau — *»Die Zeit des Schweigens ist vorbei! Aber wie weit sollen wir gehen, Jean-Luc?«*

| Option | benötigt | Effekte (Einfluss/Verdacht) | Ziel | Feedback |
|--------|----------|------------------------------|------|----------|
| „Wir sind alles! Erklären wir uns zur Nationalversammlung und brechen das Ständesystem." | `quelle_sieyes` | +20 / +15 | → Ballhaus | Mirabeau nickt entschlossen. Der Dritte Stand folgt deinem Ruf — ihr marschiert ins Ballhaus! |
| „Wir sollten den König um Gnade bitten und auf gütliche Einigung hoffen." | — | −15 / −5 | bleibt | Die Abgeordneten raunen enttäuscht. Du verlierst politisches Gewicht. |

**Ausgänge:** Ballhaus

### 5.3 Raum B — 📍 Ballspielhaus (Salle du Jeu de Paume)

> „Ein kahler Raum, eigentlich für den Sport des Adels. Regen prasselt gegen die Fenster.
> Hunderte Abgeordnete drängen sich um Jean-Sylvain Bailly."

**Artefakt:** `quelle_ballhaus` — 📜 Entwurf des Ballhausschwurs
> »…sich niemals zu trennen und überall zusammenzutreten, wo die Umstände es erfordern,
> bis die Verfassung errichtet ist…«

**NPC:** Königlicher Herold (Marquis de Dreux-Brézé) — *»Der König befiehlt Ihnen, diesen Saal sofort zu räumen!«*

| Option | benötigt | Effekte | Feedback |
|--------|----------|---------|----------|
| Tritt vor: „Wir weichen nur der Gewalt der Bajonette!" | `quelle_ballhaus` | +30 / +40 | Der Herold weicht bleich zurück. Die Revolution hat ihre rechtliche Geburtsstunde — du wirst als Held gefeiert! |

**Ausgänge:** Sitzungssaal

### 5.4 Auswertung der Welt

- **Abi-Zusammenfassung:** „Die Phase von 1789 zeigt die Transformation von unorganisiertem Protest hin zur
  institutionalisierten Revolution durch die Nutzung aufklärerischer Schriften (Sieyès)."
- **Dalton-Aufgabe:** „Mappe dein Vorgehen als Charakter: Inwiefern halfen dir die zeitgenössischen
  Quellen-Artefakte, das Risiko deiner Entscheidungen gegenüber der königlichen Autorität abzuwägen?"

---

## 6. Inhaltliche Erweiterungs-Roadmap (Welten-Backlog)

| Welt | Epoche | Quelle(n) | Status |
|------|--------|-----------|--------|
| Welt 1 | Paris/Versailles 1789 | Sieyès, Ballhausschwur | ✅ implementiert (v1.0) |
| Welt 2 | Frankfurt 1848/49 (Paulskirche) | Struve-Flugblatt, Kaiserkronen-Brief | ✅ implementiert (v2.0) |
| Welt 3 | Berlin 1933 (Krolloper) | Otto-Wels-Rede, Ermächtigungsgesetz | ✅ implementiert (v2.0) |
| Welt 5 | Leipzig 1989 (Nikolaikirche/Ring) | Aufruf zur Gewaltlosigkeit | ✅ implementiert (v2.1) |

---

## 7. Entschiedene Designfragen (v1.0)

- [x] **Comte-de-Vance-Pfad:** vollständig enthalten — eigener NPC *Graf von Artois* im Saal, *König Ludwig XVI.* im Ballhaus (rollengefiltert über `identity_for_role`).
- [x] **Verdachts-Spielende:** harter Game-Over bei `suspicion ≥ 80`; Warnung + kritisches Blinken ab `≥ 50` (gemäß Spec).
- [x] **Wiederbetretbare Räume:** NPC-Entscheidungen sind wiederholbar (kein `npcState`-Lock).
- [x] **Persistenz:** bewusst sitzungsbasiert (kein `localStorage` in v1.0).

---

## 8. Änderungsprotokoll (Changelog)

| Datum       | Version | Änderung                                                                 |
|-------------|---------|--------------------------------------------------------------------------|
| 2026-06-17  | 0.1.0   | Projekt angelegt; ARCHITEKTUR.md, PROTOKOLL.md, TODO.md erstellt; Welt 1789 spezifiziert. |
| 2026-06-17  | 1.0.0   | `index.html` implementiert: Engine, 3-Spalten-UI, Gating, beide Rollen, Game-Over, Auswertung + Dalton-Export. QA im Browser bestanden (0 Konsolenfehler). |
| 2026-06-17  | 2.0.0   | Welt-Auswahlmenü; 2 neue Welten (1848/49 mit Eduard & Gustav, 1933 mit Otto); `localStorage`-Autosave + „Fortsetzen"; Zertifikat-Download (.txt/Blob); Entscheidungs-Protokoll; modulneutraler Game-Over-Text. Gustav-Branche ergänzt & austariert. QA bestanden (0 Konsolenfehler). |
| 2026-06-17  | 2.1.0   | Welt 5 (Leipzig 1989) mit 2 Rollen (Sabine / Oberstleutnant Wagner); Quellen-Artefakt „Aufruf zur Gewaltlosigkeit" schaltet sowohl Sabines gewaltlosen Durchbruch als auch Wagners Schießbefehl-Verweigerung frei. Chronik nun lückenlos (1789–1989). QA bestanden (0 Konsolenfehler). |

---

## 9. Definition of Done (Welt 1)

Eine Welt gilt als fertig, wenn:

1. Rollenauswahl funktioniert und Startstats korrekt gesetzt werden.
2. Alle Räume betretbar, alle Artefakte aufhebbar und im Inventar sichtbar sind.
3. Quellen-Gating greift (gesperrte Option erst nach Artefakt wählbar).
4. Alle NPC-Choices Effekte + Feedback korrekt anwenden und ggf. Räume wechseln.
5. Krisenschwellen (Einfluss < 20, Verdacht > 70) visuell signalisiert werden.
6. Die Auswertung mit Abi-Wissen + kopierbarer Dalton-Aufgabe erscheint.
7. Keine Konsolenfehler; responsives 3-Spalten-Layout (lg) → gestapelt (mobil).
