# 🏛️ Chronik-Engine — Geschichts-RPG (Abitur · Dalton-Plan)

Ein textbasiertes **Serious RPG** zur Geschichts-Abiturvorbereitung. Die Lernenden übernehmen
historische Rollen, durchschreiten Räume, sammeln **Quellen-Artefakte** und schalten damit
abiturrelevante, quellengestützte Dialogentscheidungen frei. Jede Entscheidung verschiebt
**Einfluss** und **Verdacht/Risiko** und wird selbstberichtigend kommentiert.

Single-File-Anwendung (`index.html`) · Vanilla JavaScript · Tailwind CSS (CDN) · **keine Build-Tools**.

## Inhalt (lückenlose Oberstufen-Chronik 1789–1989)

| Modul | Epoche | Rollen |
|------|--------|--------|
| 0 | Paris & Versailles 1789 (Französische Revolution) | Jean-Luc (3. Stand) · Comte de Vance (Adel) |
| 1 | Frankfurt 1848/49 (Einheit & Freiheit) | Eduard (liberal) · Gustav (radikal) |
| 3 | Krolloper 1933 (Ende von Weimar) | Otto (SPD) |
| 5 | Leipzig 1989 (Friedliche Revolution) | Sabine (Bürgerrechtlerin) · Oberstltn. Wagner (Stasi) |

## Features

- **Quellen-Gating:** abiturrelevante Optionen sind erst nach Quellenarbeit wählbar (sonst 🔒).
- **Rollenspezifische NPCs** je Perspektive (`identity_for_role`).
- **Warnsystem:** Verdacht ≥ 50 % blinkt rot, **≥ 80 % = Game Over** mit Epochen-Reflexion.
- **`localStorage`-Autosave** mit „Fortsetzen"-Button im Hauptmenü.
- **Dalton-Mappen-Zertifikat** als `.txt`-Download (Name, Rolle, Epoche, Entscheidungen, Statistik, Abitur-Wissen, Reflexionsaufgabe).

## Lokal starten

Reine statische Datei — einfach `index.html` im Browser öffnen, oder:

```bash
python3 -m http.server 4139
# → http://localhost:4139
```

## Deployment (Vercel)

Statisches Projekt, **kein Build-Step**. Beim Import in Vercel:

- **Framework Preset:** `Other`
- **Build Command:** *(leer lassen)*
- **Output Directory:** `.` (Projektwurzel)

> **Hinweis für `cmaix`-Deployments:** Falls die Deployment-URL den Zugriff verweigert, liegt das
> i. d. R. an zwei gestapelten Schutzschichten im Vercel-Projekt — **Git Author Validation** und
> **Deployment Protection / SSO**. Beide unter *Project → Settings* prüfen (nicht nur eine),
> damit die App auf den Schüler-iPads ohne Login läuft.

## Projektdokumentation

- [ARCHITEKTUR.md](ARCHITEKTUR.md) — Engine-Architektur & Datenmodell
- [PROTOKOLL.md](PROTOKOLL.md) — Game Design Document & Changelog
- [TODO.md](TODO.md) — Bauplan & offene Punkte

---
*Erstellt für das Dalton-Konzept am Gymnasium Alsdorf.*
