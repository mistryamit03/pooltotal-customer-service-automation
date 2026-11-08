# POOL Total | Kurzbericht zur Arbeitsprobe

**Rolle:** Process Automation Manager / AI & Workflow Specialist  
**Prototyp:** Kundenservice-Automatisierung mit n8n und fiktiven Testdaten

## Aufgabe 1: Rückfragen, Annahmen und Prozess

**Fünf priorisierte Rückfragen**

1. **Freigabe:** Welche Antworten dürfen automatisch an Kunden gehen, und was muss immer ein Mitarbeiter freigeben?
2. **Identität:** Wie prüfen wir die Identität eines Kunden, bevor wir Bestelldaten herausgeben? Reicht die E-Mail-Adresse?
3. **Datenquelle:** Welche Quelle ist für den Versandstatus führend, JTL-Wawi, JTL-WMS oder die Tracking-Daten von DHL und GLS?
4. **Fehler:** Was soll passieren, wenn ein System nicht erreichbar ist: erneut versuchen oder sofort an einen Mitarbeiter geben?
5. **Storno:** Gibt es feste Regeln für Stornos und Erstattungen, zum Beispiel bis zu welchem Status ein Storno möglich ist und wer erstatten darf?

**Annahmen und Entscheidungen**

- **Menschliche Freigabe:** Der Workflow erstellt nur Entwürfe und Aufgaben. Mitarbeitende prüfen alle Antworten.
- **Identität:** Nur für diesen Test gelten Bestellnummer und passende Absender-E-Mail als Zuordnung. Das reicht nicht für den Echtbetrieb.
- **Daten und Fehler:** Aussagen stammen aus `test_bestellungen`. Bei einem simulierten Timeout entsteht sofort eine Prüfaufgabe, ohne automatischen neuen Versuch.
- **Storno und Erstattung:** Es werden keine Bestellungen geändert und keine Stornos oder Erstattungen ausgeführt oder bestätigt.

**Prozess:** Nachricht laden → Nachrichten-ID prüfen → Bestelldaten suchen → Absender prüfen → Anliegen erkennen → Entwurf und/oder Aufgabe erstellen → Ergebnis prüfen → speichern → Übergabe zur Prüfung.

**Umfang:** Sechs Testbestellungen, neun Eingänge und der zusätzliche Test E10; Regeln, Entwürfe, Aufgaben und Duplikatschutz. **Nicht umgesetzt:** echte JTL-, E-Mail- und Ticket-Anbindung, automatisches Senden oder Ändern von Bestellungen.

**Regeln statt KI im Workflow:** Die bekannten Testfälle lassen sich mit festen JavaScript-Regeln nachvollziehbar prüfen. Ein LLM ist im laufenden Workflow nicht eingebunden.

## Aufgaben 2 und 3: Umsetzung und Tests

- **Erster Lauf:** 9 Eingänge, 8 Ergebnisse. E09 ist ein Duplikat von E01 (gleiche Nachrichten-ID M01).
- **Zweiter Lauf:** 0 neue Ergebnisse. Die Tabelle blieb bei 8 gespeicherten Ergebnissen.
- **Zusatztest E10:** Ohne Bestellnummer wird keine Bestellung geraten. Der Workflow fragt nach und erstellt eine interne Aufgabe.
- **Korrektur E08:** Antwort zu Storno und Erstattung verbessert, M08 erneut verarbeitet und Ergebnis geprüft.

Die tatsächlichen Antworten, Aufgaben, Abweichungen und Screenshots stehen in [`TESTERGEBNISSE.md`](TESTERGEBNISSE.md). Alle Entwürfe brauchen eine Prüfung durch Mitarbeitende.

## Aufgabe 4A: Verbindung mit echten Systemen

**Gesichert:** Laut Aufgabe liegen Bestell- und Versanddaten im Umfeld von JTL-Wawi, JTL-Shop und JTL-WMS. Laut Gespräch nutzt POOL Total auch n8n und Microsoft Teams. Dieser Prototyp hat keine echte Verbindung zu diesen Systemen.

**Nur weil der Prototyp mit Testdaten funktioniert, bedeutet das nicht, dass auch die Verbindung zu echten Systemen funktioniert.**

- **JTL:** Benötigt werden Bestellnummer, Kunden-E-Mail, Artikel, Teilversand, Versandstatus und Tracking. Zu klären: Datenquelle, API oder freigegebene Schnittstelle, Aktualität und Leserechte.
- **E-Mail / Marktplätze:** Benötigt werden Nachrichten-ID, Absender, Text, Zeit und Verkaufskanal. Zu klären: Postfach, Zugang und erlaubte Antwortwege für Amazon, eBay und Kaufland.
- **Interne Aufgaben:** Benötigt werden Anliegen, Bestellnummer, Prüfgrund und zuständige Person. Zu klären: Zielsystem, Zugriffsrechte und wer Aufgaben übernimmt.

## Aufgabe 4B: Drei Schritte vor dem produktiven Einsatz

1. **Datenschutz:** DSGVO, notwendige Kundendaten, Zugriffe, Löschfristen und sichere Identitätsprüfung klären.
2. **Freigabe und Übergabe:** Alle Antworten prüfen lassen. Zuständige Mitarbeitende, Aufgabenwege und eine kurze Anleitung festlegen.
3. **Fehlerüberwachung:** Weitere Fälle testen. Fehler, Duplikate und fehlende Daten erkennen, Meldungen an das Team senden und sicher stoppen.

## Aufgabe 4C: Zwei messbare Erfolgskriterien

**KPI 1: Zeit spart pro Anfrage**  
Bearbeitungszeit ohne und mit Workflow vergleichen. Prüfung und Korrekturen zählen mit.  
**Formel:** (Zeit ohne Workflow − Zeit mit Workflow) / Zeit ohne Workflow × 100.

**KPI 2: Korrekt vorbereiteten Anfragen (%)**  
Anteil geprüfter neuer Anfragen mit richtiger Antwort, geschützten Daten und notwendiger interner Aufgabe.  
**Formel:** Korrekte Fälle / alle geprüften neuen Fälle × 100.

**Messung:** Es liegen keine echten Ist-Werte vor. Beide Werte müssen erst im Betrieb mit vergleichbaren Anfragen gemessen werden.

## Aufgabe 4D: Nächste Automatisierung: Adressprüfung vor dem Versand

Bei Bettermile habe ich über 150 Fahrermeldungen geprüft. Die meisten Probleme hatten mit falschen oder unvollständigen Adressen zu tun. Dabei habe ich gesehen, wie viel zusätzliche Arbeit solche Fehler verursachen können.

Deshalb würde ich bei POOL Total als Nächstes eine automatische Adressprüfung vor dem Versand vorschlagen.

Der Workflow erkennt fehlende oder auffällige Adressdaten und erstellt eine Aufgabe für einen Mitarbeiter. Die Adresse wird nicht automatisch geändert.

**Mein Ziel:** Mögliche Fehler früh erkennen, Fehlzustellungen vermeiden und die Arbeit im Kundenservice und Versand reduzieren.

## Offene Punkte und Grenzen

- Echte Zugänge, erlaubte Antwortkanäle, Identitätsprüfung, zuständiges Team und technische Fehler-Meldungen sind noch zu klären.
- E05 ist ein **simulierter** Timeout. Gleichzeitige Ausführungen und viele unbekannte Formulierungen wurden nicht getestet. Eine echte Ticket-Übergabe ist nicht eingebaut.

## Zeitaufwand und KI-Nutzung

**Zeitaufwand:** Bisher ca. **45–48 Stunden** für Planung, Aufbau des Workflows, Fehlersuche, Tests, Dokumentation und Vorbereitung der Bildschirmdemo (Stand: 08.11.2026; die Aufnahme selbst ist nicht eingerechnet).

**KI-Unterstützung:** Ich habe ChatGPT für die Planung, Hilfe bei JavaScript-Code und n8n, Fehlersuche, Testplanung, Prüfung von Screenshots, Dokumentation und die finale Kontrolle genutzt.

**Meine eigene Arbeit:** Ich habe die fünf Rückfragen priorisiert und begründet. Ich habe Annahmen und Regeln für die sichere Bearbeitung gewählt. Den Workflow habe ich selbst in meinem lokalen n8n aufgebaut und konfiguriert. Ich habe die Tests ausgeführt, Ergebnisse geprüft, Fehler untersucht und Korrekturen umgesetzt. Auch den zusätzlichen Testfall E10 habe ich im Workflow getestet. KI-Vorschläge habe ich geprüft und bei Bedarf angepasst.

**KI im Workflow:** Im laufenden Workflow wird kein KI-Modell verwendet. Die Verarbeitung nutzt feste JavaScript-Regeln und fiktive Kundendaten.
