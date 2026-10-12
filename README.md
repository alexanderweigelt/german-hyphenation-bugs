# Falsche deutsche Silbentrennung in Chrome, Safari und Firefox

Mit `lang="de"` und `hyphens: auto` trennen alle drei großen Browser viele zusammengesetzte deutsche Wörter an der falschen Stelle.

👉 **Beispielseite:** [alexanderweigelt.github.io/german-hyphenation-bugs](https://alexanderweigelt.github.io/german-hyphenation-bugs/) zeigt die Fehler direkt im eigenen Browser.

Stand: 12. Oktober 2026. Eigene Messungen: macOS 26.6.2 mit Chrome 155, Safari 26.6.2 und Firefox Developer Edition 158. Andere Systeme und macOS-Versionen sind nicht geprüft.

## Das Problem

Zusammengesetzte Wörter werden nach dem amtlichen Regelwerk zwischen ihren Bestandteilen getrennt (§ 85). Die Browser setzen die Trennstelle aber oft um einen Buchstaben daneben:

| Wort | Browser trennt | Richtig |
|---|---|---|
| Wissensportal | Wissen-sportal | Wissens-portal |
| Klimafaktor | Klimaf-aktor | Klima-faktor |
| Wärmeinhalt | Wärmein-halt | Wärme-inhalt |
| Wasserstofferzeugung | Wasserstof-ferzeugung | Wasserstoff-erzeugung |
| polytheistisch | polyt-heistisch | poly-theistisch |

Meist beginnt der zweite Wortteil mit einem Vokal, oder das Fugen-s rutscht in den zweiten Teil. Sichtbar wird der Fehler nur, wenn der Zeilenumbruch genau auf diese Stelle fällt. Das hängt von Breite, Schrift und Umbruchstrategie ab; bei gleichen Bedingungen ist er reproduzierbar.

Ausgangspunkt war eine Überschrift, in der Chrome ein solches Wort falsch trennte und Safari gar nicht. Die Frage war, warum die Browser unterschiedlich reagieren.

## Ursache

**Chrome und Safari auf macOS** haben keine eigenen deutschen Trenndaten. Beide fragen die Trennstellen beim Betriebssystem ab (Apple CoreFoundation, `CFStringGetHyphenationLocationBeforeIndex`). Das ist im Quelltext von Chromium und WebKit belegt. Ein Chromium-Entwickler schrieb schon 2017, dass Chrome eine Korrektur automatisch übernimmt, sobald macOS sie liefert. Beheben kann den Fehler hier also nur Apple. Eine direkte Abfrage der Systemfunktion liefert dieselben falschen Stellen, die Chrome und Safari anzeigen (gemessen).

**Firefox** bringt eigene deutsche TeX-Trennmuster aus dem Jahr 2011 mit, auch in den aktuellen Versionen 157 und 156 (belegt im Quelltext). Die Autoren der Muster haben viele dieser Fehler seit 2021 korrigiert, Firefox hat die neuen Muster nicht übernommen. Mozilla kennt das Problem: In den Bugs 2001880 („Permafröste“) und 2079326 („polyt-heistisch“) nennt ein Mozilla-Entwickler die alten Muster als Ursache und empfiehlt, sie zu aktualisieren.

**Gemeinsame Wurzel (abgeleitet, nicht bewiesen):** Bei 14 949 von 14 984 deutschen Wörtern liefert macOS genau dieselben Stellen wie Firefox' Muster von 2011. Wahrscheinlich nutzt Apple dieselben oder fast dieselben Muster. Woher Apples Daten stammen, ist öffentlich nicht dokumentiert.

**Warum Safari oft anders aussieht:** Safari setzt `text-wrap: pretty` so um, dass es Trennungen möglichst vermeidet. Ohne `pretty` wählt Safari dieselben Umbrüche wie Chrome und Firefox und kennt dieselben falschen Stellen (gemessen). Dass Safari nicht trennt, ist also kein Gegenbeweis.

## Wie häufig ist das?

In einer Liste von 14 984 deutschen Wortformen aus 15 Wikipedia-Artikeln bietet macOS bei mindestens 117 eine falsche Stelle an, also etwa bei jedem 130. Wort. Fast immer liegt der Fehler an einer Wortfuge. Gezählt wurden die Abweichungen von den korrigierten Mustern; ob eine Stelle wirklich falsch ist, wurde von Hand bewertet. Das ist eine Untergrenze für diese Liste, keine Fehlerquote für den deutschen Wortschatz.

Dass die Fehlerklasse bekannt ist, bestätigen die Autoren der Trennmuster selbst: Ihre Dokumentation nennt häufige Trennfehler an Wortfugen als Schwäche der alten Muster.

Warum es trotzdem kaum Meldungen an die Browser gibt, sagt keine Quelle. Naheliegend ist (abgeleitet): Auf macOS trennen alle drei Browser gleich, die Fehler sind nur bei bestimmten Breiten sichtbar, und Meldungen an Apple sind nicht öffentlich.

## Was mit HTML und CSS geht und was nicht

Eine richtige Trennung lässt sich mit CSS nicht erzwingen, weil keine Engine die richtigen Stellen an diesen Fugen kennt. CSS kann Trennstellen nur unterdrücken.

| Ansatz | Ergebnis |
|---|---|
| `hyphens: manual` für Überschriften | Kein falscher Trennstrich mehr. Lange Wörter brechen dafür ohne Strich an beliebiger Stelle um, etwa „Wissensp/ortal“. |
| Mindestlängen: `hyphenate-limit-chars: auto 6 3` und zusätzlich `-webkit-hyphenate-limit-before: 6; -webkit-hyphenate-limit-after: 3` | Hilft nur gegen kurze falsche Anfänge. `Wissen-sportal` und `Klimaf-aktor` bleiben möglich. Safari 26 versteht nur die `-webkit-`-Variante, Chrome und Firefox nur die Standard-Eigenschaft (gemessen). |
| `text-wrap: pretty` oder `balance` | Keine verlässliche Abhilfe, wirkt je Browser anders. |
| `<wbr>` an der Fuge | Bricht an der richtigen Stelle, aber ohne Strich. Muss für jedes Wort gepflegt werden. |
| Trennung beim Rendern mit aktuellen Mustern | Kandidat, nicht in einer echten Anwendung getestet. Nutzt algorithmisch eingefügte weiche Trennzeichen und ist deshalb, wie JavaScript-Silbentrennung, die letzte Option. |

**Empfehlung:**

1. `hyphens: auto` vorerst beibehalten.
2. Die Fehler bei Apple (Feedback Assistant) und Mozilla (Bug 2079326) melden. Wann eine Korrektur kommt, lässt sich nicht beeinflussen.
3. Nur wenn falsche Trennungen in Überschriften nicht hinnehmbar sind: dort `hyphens: manual`, mit den Notumbrüchen als bewusstem Preis. Wird korrekte Trennung verlangt, bleibt nur die Trennung beim Rendern mit aktuellen Mustern.

## Zur Beispielseite

Die [Beispielseite](https://alexanderweigelt.github.io/german-hyphenation-bugs/) ([`index.html`](index.html)) besteht nur aus HTML und CSS ([`css/style.css`](css/style.css)), ohne JavaScript und ohne weiche Trennzeichen. Sie zeigt zwölf Wörter in Boxen mit fester Breite, die unter macOS 26.6.2 in allen drei Browsern an der falschen Stelle umbrechen (gemessen am 12.10.2026). Dazu kommen alle Trennstellen des eigenen Browsers, eine ziehbare Überschrift, `pretty` im Vergleich und die CSS-Abhilfen. Die Breiten sind in `ch` angegeben und hängen von der Schrift ab; auf anderen Systemen kann der Umbruch woanders liegen.

## Quellen

- Regelwerk: [§ 85](https://grammis.ids-mannheim.de/rechtschreibung/6215), [§§ 86–89](https://grammis.ids-mannheim.de/rechtschreibung/6216)
- Chrome: [Apple-Pfad in Chromium 155](https://github.com/chromium/chromium/blob/155.0.8059.40/third_party/blink/renderer/platform/text/apple/hyphenation_apple.cc), [Plattformauswahl](https://github.com/chromium/chromium/blob/155.0.8059.40/third_party/blink/public/public_features.gni#L13), [Chromium 41292906, Kommentar 7](https://issues.chromium.org/issues/41292906#comment7), [Chromium 40933841, Kommentar 5](https://issues.chromium.org/issues/40933841#comment5)
- Safari: [WebKit HyphenationCF.cpp](https://github.com/WebKit/WebKit/blob/7e93855802b3579fc78fdfbc75efddf97562258a/Source/WebCore/platform/text/cf/HyphenationCF.cpp), [WebKit 166757, Kommentar 2](https://bugs.webkit.org/show_bug.cgi?id=166757#c2), [text-wrap: pretty in WebKit](https://webkit.org/blog/16547/better-typography-with-text-wrap-pretty/)
- Apple: [CFStringGetHyphenationLocationBeforeIndex](https://developer.apple.com/documentation/corefoundation/cfstringgethyphenationlocationbeforeindex(_:_:_:_:_:_:)), [Feedback Assistant](https://developer.apple.com/feedback-assistant/)
- Firefox: [Herkunft der Muster](https://github.com/mozilla-firefox/firefox/blob/FIREFOX_157_0_1_RELEASE/intl/locales/de-1996/hyphenation/LICENSE), [Versionsgeschichte](https://github.com/mozilla-firefox/firefox/commits/main/intl/locales/de-1996/hyphenation/hyph_de-1996.dic), [Mozilla 2001880](https://bugzilla.mozilla.org/show_bug.cgi?id=2001880), [Mozilla 2079326](https://bugzilla.mozilla.org/show_bug.cgi?id=2079326)
- Trennmuster: [dehyph-exptl-Dokumentation](https://ftp.fau.de/ctan/language/hyphenation/dehyph-exptl/dehyph-exptl.pdf), [DANTE: Trennmuster](https://wiki.dante.de/trennmuster/trennmuster), [tex-hyphen, Update vom 20.02.2025](https://github.com/hyphenation/tex-hyphen/commit/6d38cfcb04af70a57607bd8f698938755f1b68b3)
- CSS: [CSS Text 4: hyphenate-limit-chars](https://www.w3.org/TR/css-text-4/#propdef-hyphenate-limit-chars), [CSS Text 4: text-wrap-style](https://www.w3.org/TR/css-text-4/#text-wrap-style)
