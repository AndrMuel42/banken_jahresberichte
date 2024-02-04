# 25 Jahre UBS - eine Bank wie die Schweiz?
Analyse aller UBS-Jahresberichte (Jahres-Reviews, ohne Zahlenteil) von 1999 bis 2020, mittels Wortzählungen und Häufigkeitsanalysen.

## 0 Überblick
- Ausgangsthese: Eine Bank wie die Schweiz? Tatsächlich. Was die UBS beschäftigt, beschäftigt auch die Schweiz. Seit der Gründung der Bank 1998.
- Relevanz und Neuigkeit? Häufigkeitsanalysen in Texten entlang der Zeitachse - dieser Ansatz wurde in datenjournalistischen Projekten schon mehrfach angewendet. Allen voran simpel und doch eindrucksvoll vom "Atlantic", der sämtliche "State of the Union"-Reden der US-Präsidenten seit dem 18. Jahrhundert auf Kernbegriffe hin untersuchte und damit eine knappe, und doch reichhaltige Geschichte der USA in wenigen Grafiken abbildete. Eine entsprechende datenjournalistische Aufarbeitung von Bankberichten ist mir nicht bekannt. (Wenngleich es eine Reihe von Projekten gibt, um Geschäftsberichte auszuwerten - etwa ein Citizen-Science-Projekt, um die neuen Nachhaltigkeitsberichte systematisch zu untersuchen, welche Schweizer Unternehmen ab dem Jahr 2024 anfertigen müssen.)
- Aufwand und Ertrag: Für die Grundgeschichte vertretbar, zuletzt 4 bis 5 Arbeitstage. Eine Ausweitung auf die Geschäftsberichte der Vorgängerbanken der UBS - Schweizerischer Bankverein und Schweizerische Bankgesellschaft - erhöhte den Zeitaufwand signifikant, da keine gut digitalisierten und leicht zugänglichen Versionen vorliegen. Ich müsste einerseits alle Geschäftsberichte (respektive Auszüge daraus) eigenhändig abfotografieren, andererseits diese Bilder via Tesseract wieder in Text umwandeln. Bei Tests stellte einerseits die Bildqualität ein Problem dar, andererseits auch der Setup von Tesseract. Die Qualität der Texterfassung aus den Bildern verbesserte sich bei Tests deutlich, nachdem ich sowohl die Bildqualität erhöhte (eng gewählter Bildausschnitt, sehr flach anliegende Seiten, usw.), die Bilder nachbearbeitete (Hoher Bildkontrast, Umwandlung in Schwarzweiss-Bilder, eng gewählter Bildausschnitt) und schliesslich auch das Textdokument nachbearbeitete (mit Bindestrich getrennte Wörter zusammenführen, Wörterbücher zur Prüfung und teilweise automatischen Korrektur des Texts nutzen bei häufigen Fehlern...). Aber es zeigte sich, dass ein grosser Zeitaufwand für manuelle Arbeit verbleiben würde, so dass das Projekt nicht in nützlicher Frist abgeschlossen werden könnte
- Knackpunkt: Die Resultate könnten erwartbar, banal sein.

## 1 Daten beschaffen
Die Beschaffung der Daten war bei der Grundgeschichte nicht allzu schwierig, da die UBS sämtliche Geschäftsberichte seit ihrer Gründung [online verfügbar](https://www.ubs.com/global/de/investor-relations/financial-information/annual-reporting/ar-archive.html) hält. Herausfordernder war es, die richtige Serie an Dokumenten für meine Fragestellung auszuwählen, denn die UBS führte über die Jahre teilweise ziemlich unterschiedliche Berichte: Jahres-Reviews, vollständige Jahresberichte, dazu GV-Einladungen sowie Finanz-, Vergütungs- und Jahresberichte. Ich entschied mich dafür, die Reviews auszuwerten. Einerseits sind diese textlastig und enthalten einen relativ grossen Teil an "freien" Textpassagen, die sich von Jahr zu Jahr unterscheiden und nicht einem engen Korsett folgen müssen. Andererseits sind die Reviews grundsätzlich von Anfang bis in die heutige Zeit publiziert worden. Eine Ausnahme bilden leider die neuesten Jahre 2021 und 2022, als die UBS unter ihrem digitalaffinen CEO Ralph Hamers den Jahresbericht in erster Linie digital veröffentlichte. Ein englisches PDF des Jahresberichts wird zwar weiterhin zum Download angeboten, aber keine deutsche Jahres-Review mehr. Ich testete ein zwei Varianten, um diese beiden Jahre trotzdem in meine Untersuchung aufzunehmen, aber die Vergleichbarkeit des Textkorpus war einfach nicht gegeben. (In einem späteren Schritt werde ich mir wohl die englischen Versionen anschauen, weil diese eine bessere Vergleichbarkeit bis heute bieten).

## 2 Daten reinigen, PDF-Files aufbereiten
Ich extrahierte den Text aus den PDF-Files über eine for-Schleife und speicherte den Text in einem simplen Dataframe ab. Einzelne Jahresberichte musste ich manuell etwas anpassen, weil mein Converter Mühe bekundete, wenn zu viele bildlastige Seiten im Bericht enthalten waren. Ich arbeitete mit [PyMuPDF](https://pymupdf.readthedocs.io/en/latest/), weil PyPDF2 nicht die gewünschte Qualität hinbekommen hat. Insbesondere der Jahresbericht von 2005 brauchte einige Anläufe, weil er zahlreiche Bilder enthielt, auf denen Personen Plakate mit Text hielten - das verwirrte den Converter.

## 3 Daten reinigen, Nachbearbeitung der Textfiles
Die PDF-zu-Text-Konversion funktionierte grundsätzlich sehr gut. Ich filterte im ersten Arbeitsschritt gleich auch Zeilenumbrüche ("\n") und Satzzeichen am Wortende heraus. Anschliessend wandelte ich die Textblöcke in Listen mit einzelnen Wörtern um, welche ich dann weiter bearbeitete. Ich schrieb einige Loops, um bei Zeilenende aufgetrennte Wörter wieder zusammenzufügen, und um Zahlen, weitere Satzzeichen sowie eine lange Reihe an nichtssagenden Wörtern wegzulassen. Die Basisliste an Wörtern hierfür erhielt ich von Simon Schmid; ich adaptierte sie daraufhin für meine Zwecke noch etwas, um allen voran die immergleichen Ausdrücke wie "Mio.", "CHF" oder "Mrd." auszusondern. Diese kommen im Zahlenteil meiner Jahresreviews gehäuft vor und drohten das Ergebnis zu verfälschen.

## 4 Daten analysieren
Ich schrieb nun in einem TXT-File eine erste Liste an Suchbegriffen, welche ich in den Jahresberichten zählen wollte. Ich setzte auf eine zweistufige Struktur von Oberbegriffen/Klassen, welche einen bis rund 30 Unterbegriffe enthielt; dazu zählten indes auch alle Deklinationsformen von Adjektiven. Ich musste einige Runden lang iterieren und mich in die Jahresberichte selbst vertiefen, bis ich die für meine Zwecke taugliche Kombination von Suchbegriffen fand. Kenntnisse der jüngeren Bankengeschichte der Schweiz sowie einige Recherchen im NZZ-eigenen Archiv halfen bei diesem Prozess.

## 5 Daten visualisieren
Im ersten Schritt arbeitete ich mit Matplotlib, bis ich mir ein genaues Bild der Daten gemacht hatte. Als ich die richtige Kombination an Suchbegriffen gefunden und den Datensatz ausgewertet hatte, exportierte ich mir die Häufigkeitsdaten in ein CSV-File, welches ich anschliessend mit dem NZ-eigenen Grafiktool Q aufbereitete. Für diese erste Datengeschichte zu den UBS-Berichten reicht dies vollumfänglich aus. Wenn ich im zweiten Schritt - analog zur Atlantic-Geschichte - den Zeithorizont nochmals bedeutend ausweiten kann, könnte sich auch eine eigenständigere Gestaltungsform anbieten, die es den Leserinnen und Lesern erlaubt, sich selbständig durch den Datensatz zu bewegen; Suchbegriffe auszuwählen und zu vergleichen etc.

## 6 Ergänzen durch klassische Recherche
Da ich die vergangenen vier Jahre das Bankendossier der NZZ geführt habe, konnte ich die Analyse grösstenteils selber vornehmen, mitunter mit Rückgriff auf historische Arbeiten oder frühere Artikel zu einzelnen der Fokusthemen.

## 7 Dokumentieren Code und statistische Annahmen

## 8 Link auf Publikation
Der Artikel ist noch nicht erschienen.

## 9 Aufwandslogbuch
Vorbereitung Themenwahl: 2h
Datenbeschaffung: 2h
Datenreinigung: ca. 12h (einiges Trial and Error nötig)
Datenanalyse: 12h (mehrere Durchläufe mit Suchbegriffen nötig)
Datenvisualisierung: 2h
Schreiben Artikel & Dokumentation: ca. 8h

