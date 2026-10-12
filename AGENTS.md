# AGENTS.md

Gemeinsame Anleitung für Coding Agents (Claude Code, Codex und andere) in diesem Projekt.

## Projekt in Kürze

- Inhalt: Beschreibung und Beispiele dafür, dass Chrome, Safari und Firefox deutsche Komposita mit `hyphens: auto` falsch trennen (etwa `Wissen-sportal`), samt Ursache, Belegen und bewerteten Abhilfen.
- [README.md](README.md) enthält das vollständige Ergebnis mit Quellen. [index.html](index.html) ist die Beispielseite für GitHub Pages.
- Sprache aller Dokumente: Deutsch.

## Aufbau

| Pfad | Inhalt |
|---|---|
| `index.html` | Beispielseite mit Erklärung, Fehlerfällen und Abhilfen |
| `css/style.css` | Einziges Stylesheet |
| `404.html`, `favicon.ico`, `icon.*`, `robots.txt` | Statische Beigaben für GitHub Pages |
| `README.md` | Ergebnis der Analyse mit Quellen und Grenzen |

## Arbeitsregeln

1. Die Seite bleibt reines HTML und CSS: kein JavaScript, kein Build-Schritt, keine weichen Trennzeichen. `lang="de"` und `hyphens: auto` bleiben gesetzt.
2. Anonymität: keine Hinweise auf die ursprünglich betroffene Seite, ihren Betreiber, ihre URL oder ihren Technikstack. Beispielwörter stammen aus der gemessenen Wortliste (z. B. Wissensportal, Klimafaktor, Wärmeinhalt).
3. Evidence First: Aussagen in README und Seite sind entweder durch eine verlinkte Quelle oder eine eigene Messung belegt oder ausdrücklich als abgeleitet gekennzeichnet. Niemals Messwerte, Quellen oder Zitate erfinden.
4. Jede eigene Messung nennt Browser und Version, Betriebssystem und Version, Schrift und relevante CSS-Werte. Der aktuelle Stand gilt für macOS 26.6.2.
5. Ändert sich ein Fehlerfall auf der Seite (Wort oder Breite), vorher in Chrome, Safari und Firefox prüfen, dass der Umbruch an der falschen Stelle landet. Chrome dafür im sichtbaren Fenster messen, nicht headless.
6. Safari nicht als Playwright-WebKit ausgeben; Playwright-Builds immer als solche benennen.
7. Untersuchungsergebnisse und Lösungsvorschläge strikt trennen: nachgewiesene Ursache, nachgewiesene Abhilfe, Lösungskandidat.
8. Keine Installation zusätzlicher Tools oder Pakete und keine kostenpflichtigen Dienste ohne vorherige Zustimmung.

## Git

Nicht committen oder pushen, solange der Mensch das nicht ausdrücklich verlangt.
