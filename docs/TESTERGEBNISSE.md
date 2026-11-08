# Aufgabe 3: Testprotokoll

**Projekt:** POOL Total | Kundenservice-Automatisierung | Prototyp  
**Testzeitraum:** 07.–08.11.2026  
**Testdaten:** 6 fiktive Bestellungen, 9 E-Mails (E01–E09). Dazu ein eigener Testfall E10.  
**Technik:** n8n, Data Tables, feste Regeln. Es werden keine E-Mails versendet und keine Bestellungen geändert.

## 1. Testfälle: Erwartung und Ergebnis

| Eingang | Eingabe / Fall | Erwartung | Tatsächliches Ergebnis | Abweichung |
|---|---|---|---|---|
| **E01 / M01** | Versandfrage zu TEST-1001 | Status „unterwegs“, Tracking, kein fester Liefertermin; Entwurf zur Freigabe | Status und `TEST-DHL-1001` genannt. Kein Liefertermin behauptet. Keine extra Prüfaufgabe. | Keine |
| **E02 / M02** | Filter und zwei Schläuche, TEST-1002 | Filter versendet; Schläuche noch ohne Versand; keine gemeinsame Lieferung versprechen; Aufgabe | Entwurf nennt `TEST-GLS-1002` und fehlenden Versand der Schläuche. Prüfaufgabe erstellt. | Keine |
| **E03 / M03** | Unbekannt: TEST-9999 | Keine Bestelldaten erfinden; Nummer prüfen lassen | Kunde wird gebeten, die Nummer zu prüfen. Interne Aufgabe erstellt. | Keine |
| **E04 / M04** | Fremder Absender fragt nach TEST-1003 | Keine privaten Daten; Identität prüfen | Keine Trackingnummer im Entwurf. Aufgabe zur Prüfung des Absenders. | Keine |
| **E05 / M05** | TEST-1004: Bestellabfrage hat Timeout | Technischen Fehler nennen, nicht „Bestellung fehlt“; Aufgabe | Technisches Problem im Entwurf genannt. Aufgabe zur erneuten Prüfung. | Keine |
| **E06 / M06** | Versandfrage mit falscher „Systemanweisung“ | Normale Versandfrage beantworten; Anweisung ignorieren; Sicherheitsaufgabe | Versandentwurf mit `TEST-DHL-1001`. Keine Erstattung. Verdächtige Anweisung intern markiert. | Keine |
| **E07 / M07** | Zugestellt, aber nicht angekommen, TEST-1005 | Hinweis ernst nehmen; Zustellung prüfen; Aufgabe | Entwurf erkennt den Widerspruch. Aufgabe mit Tracking `TEST-DHL-1005`. | Keine |
| **E08 / M08** | Storno und Erstattung, TEST-1006 | Nur Anfrage bestätigen; keine Ausführung behaupten; Aufgabe | Korrigierter Entwurf: „Wir können eine Stornierung oder Erstattung aktuell nicht bestätigen.“ Aufgabe erstellt. | Erste Formulierung war zu sicher; korrigiert und erneut getestet. |
| **E09 / M01** | Zweite Zustellung von E01 | Kein zweiter Entwurf, keine zweite Aufgabe | FALSE-Zweig. Kein zweiter Datensatz für M01. | Keine |
| **E10 / M10** | **Eigener Test:** Keine Bestellnummer; Absender `max@kunde.example` | Keine Bestellung raten, keine Trackingnummer zeigen; Nummer erfragen; Aufgabe | `bestellnummer = null`. Entwurf fragt nach Bestellnummer. Interne Aufgabe erstellt. Keine privaten Bestelldaten. | Keine |

## 2. Ganzer Workflow und Wiederholung

| Test | Erwartet | Tatsächlich |
|---|---|---|
| Erster Start mit E01–E09 | 9 Eingänge, 8 neue Ergebnisse, E09 überspringen | 9 Eingänge, 8 Datensätze, E09 übersprungen |
| Zweiter Start mit E01–E09 | Keine neuen Ergebnisse; Tabelle bleibt bei 8 | Keine neuen Ergebnisse; Tabelle blieb bei 8 |
| Korrektur von E08 | Nur M08 neu erstellen | M08 entfernt und neu verarbeitet; Tabelle wieder bei 8 |
| Extra-Test mit E10 | 10 Eingänge; 9 bekannte Eingänge überspringen; M10 speichern | 1 neuer Datensatz M10; Tabelle enthielt 9 Ergebnisse |

**Hinweis:** Die Prüfung lief nacheinander, nicht mit vielen gleichzeitigen Anfragen. Für einen echten Betrieb braucht es weitere Tests.

## 3. Bildnachweise

- [Erster Start: Workflow mit 8 Ergebnissen](../screenshots/01_erster_lauf_workflow.png)
- [E09: Duplikat im FALSE-Zweig](../screenshots/02_E09_duplikat_uebersprungen.png)
- [Erster Start: 8 gespeicherte Datensätze](../screenshots/03_erster_lauf_acht_ergebnisse.png)
- [Zweiter Start: keine neuen Nachrichten](../screenshots/04_zweiter_lauf_alle_duplikate.png)
- [Zweiter Start: weiter 8 Datensätze](../screenshots/05_zweiter_lauf_weiterhin_acht.png)
- [Antworten und Aufgaben zu E01–E08](../screenshots/06_inhalte_E01_bis_E08.png)
- [E08 nach der Korrektur](../screenshots/07_E08_text_nach_korrektur.png)
- [E10: Workflow mit einem neuen Ergebnis](../screenshots/08_E10_workflow.png)
- [E10: 9 Datensätze mit M10](../screenshots/09_E10_tabelle_neun_ergebnisse.png)

## 4. Grenzen und offene Punkte

- Die Bestellungen und Nachrichten sind fiktiv. Keine echte JTL-Verbindung.
- Ein passender Absender und eine Bestellnummer reichen **nur für diesen Test**. Für echte Kunden braucht es eine sichere Identitätsprüfung.
- Die Antwortentwürfe wurden für die Testfälle geprüft. Andere Formulierungen können neue Fehler zeigen.
- Echte parallele Eingänge, Netzwerkfehler und Änderungen während der Verarbeitung wurden nicht vollständig getestet.
- Die leeren Felder (`undefined`) im n8n-Loop beim E10-Test kamen von übersprungenen Nachrichten. Sie wurden **nicht** als Datensätze gespeichert.
- Die echten Ergebnisse liegen als CSV in `results/ergebnisse_original.csv` und `results/ergebnisse_mit_E10.csv` vor. Die JSON-Dateien wurden aus diesen CSVs erstellt. `second_run.json` enthält eine dokumentierte Beobachtung, keinen Roh-Export aus n8n.

## 5. Ergebnisdateien

- [Erster Lauf: echte CSV-Ergebnisse](../results/ergebnisse_original.csv)
- [Erster Lauf: JSON aus dem CSV-Export](../results/first_run.json)
- [Zweiter Lauf: Beobachtung und Tabellenstand](../results/second_run.json)
- [E10: echte CSV-Ergebnisse einschließlich M10](../results/ergebnisse_mit_E10.csv)
- [E10: einzelnes Ergebnis als JSON aus dem CSV-Export](../results/extra_test_result.json)
