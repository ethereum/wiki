---
name: Clearinghaus
category: 
---

### Einleitung
Bei manchen Finanzinstrumente wie ["Future"] (http://de.wikipedia.org/wiki/Future) (keine Geldtermingeschäfte) können sich die Parteien gegen Zahlungsausfälle gegenüber dem  Geschäftspartner (i.d.R. bei einem Clearinghaus) absichern. Im Wesentlichen liefert eine Clearingstelle diese Garantie über ein Verfahren, bei dem die Gewinne und Verluste, die sich aus dem börslichen Handel dieser Features über die Vertragslaufzeit ergeben, durch tägliche Abrechnung in Bargeld umgewandelt werden.
### Tägliche Abrechnung
Wie erklärt, wird die "tägliche Abrechnung" von Gewinnen oder Verlusten in Bargeld konvertiert. Dieser Vorgang wird auch ** Marktbewertung ** bezeichnet und lässt sich am Besten über ein Beispiel veranschaulichen.
Angenommen zwei Parteien gehen einen Vertrag ein, einen [Vertragsgegenstand] (http://de.wikipedia.org/wiki/Underlying) zu einem vereinbarten Preis von 100 USD in einem Monat zu kaufen/verkaufen. Um diesen Vertrag einzugehen verlangt die Clearingstelle einen Vorschuss beider Partein, die diesen vorab begleichen müssen. Also die Partei, die den Vertragsgegenstand kaufen will [(** Halter der long position **)] (http://de.wikipedia.org/wiki/Long_und_Short) und die Partei, die den Vertragsgegenstand verkaufen will [(** Halter der short position **)] (http://de.wikipedia.org/wiki/Long_und_Short).
Die Vorschusszahlung möge 5 USD betragen und wird einbezahlt in einen **margin account** des Clearinghauses, gebunden an den spezifischen Vertrag.
Während der Laufzeit des Vertrags variiert der Betrag auf dem **margin account**. Die in dem Vertrag involvierten Parteien müssen das Guthaben des **margin account** über einem bestimmten Level halten, genannt **maintenance margin requirement**,  der von dem Clearinghaus bestimmt wird und i.d.R. unterhalb der Höhe des Zahlungsvorschusses liegt.
Am Ende eines jeden Tages wird die Clearingstelle einen ** Abrechnungspreis ** bieten (in der Regel ein Durchschnittspreis aller Trades, die im Laufe des Tages passiert sind) um dann das ** Mark-to-Market-Verfahren ** (tägliche Abrechnungs) durchzuführen.
In unserem konkreten Beispiel nehmen wir an, dass die Höhe der Sicherheitsleistung, die nicht unterschritten werden darf (**maintenance margin requirement**) auf einen Betrag von 3 USD festgelegt wird und die kaufende Partei 10 Verträge eingeht. D.h. es wird eine Vorschusszahlung von 10 x 5 USD = 50 USD fällig. Wir gehen auch davon aus, dass das Geld von der Clearingstelle nicht zurückgebucht werden kann (z.B. Überschüsse). Der Initialpreis (des Future Contracts) beträgt 100 USD.
Ein vollständiges beispiel zeigt die folgende Tabelle. Es sei darauf hingeweisen, dass beide Tafeln die täglichen Salden beider Partein berichten (long position und short position).
#### Tafel A. Halter der Long Position von 10 Verträgen
|Tag (1)|Startsaldo (2)|Vorschusszahlung (3)|[Settlementpreis](http://boerse.ard.de/boersenwissen/boersenlexikon/settlement-preis-100.html) (4)| Änderung des Preises des  (5)|Gewinn/Verlust (6)|Endsaldo (7)|
|:----------:|-------------:|------:|---:|---:|---:|---:|
|0|0|50|100.0| - | - |50|
|1|50|0|99.20| -0.80| -8|42|
|2|42|0|96.00| -3.20| -32|10|
|3|10|40|101.00|5.00|50|100|
|4|100|0|103.50|2.50|25|125|
|5|125|0|103.00| -0.50| -5|120|
|6|120|0|104.00|1.00|10|130|
#### Tafel B. Halter der Short Position von 10 Verträgen
|Tag (1)|Startsaldo (2)|Vorschusszahlung (3)|[Settlementpreis](http://boerse.ard.de/boersenwissen/boersenlexikon/settlement-preis-100.html) (4)| Änderung des Preises des  (5)|Gewinn/Verlust (6)|Endsaldo (7)|
|:----------:|-------------:|------:|---:|---:|---:|---:|
|0|0|50|100.0| -| -|50
|1|50|0|99.20| -0.80|8|58
|2|58|0|96.00| -3.20|32|90
|3|90|0|101.00|5.00| -50|40
|4|40|0|103.50|2.50| -25| 15
|5|15|35|103.00| -0.50| 5|55
|6|55|0|104.00|1.00| -10|45|}
Der Tag des Vertragsabschlusses wird als'' Tag 0'' bezeichnet, an dem beide Parteien eine Vorschusszahlung von 50 USD hinterlegen. Wir nehmen an, dass am "Tag 1" der Preis des Futures auf 99,20 USD fällt, wie in Spalte 4 der Tafel A angegeben. In Spalte 5 können wir die Änderung des Preises des Futures sehen -0,80 USD (99,20 - 100) und dieser Betrag wird mit der Zahl der Verträge (10) multipliziert um in Spalte 6 den Gewinn/Verlust zu erhalten: -0,80 x 10 = - 8 USD. Der Endsaldo ist in Spalte 7 zu sehen, welcher dem Anfangssaldo des nächsten Tages entspricht. Der Endsaldo am Tag 1 für den Inhaber der Long-Position ist 42 USD und liegt somit über dem Mindestsaldo der Sicherheitsleistung (**maintenance margin**) von 3 x 10 USD = 30 USD, so dass keine Nachschuss erforderlich ist, um über diesen Wert zu kommen.
### Ethereum Clearinghaus Implementierung und Logik
* gilt es für alle Verträge? 
* welches Falg muss gesetzt werden um die Clearinghaus zu ermöglichen? 
* wie werden das Geld / Ether belastet / gutgeschrieben? Push oder Pull System? 
* Normalerweise verwenden Clearinghäuser einen durchschnittlichen Tagespreis (nicht den Abschlusspreis). Wie ist das umsetzen? Dies ist wichtig um Preismanipulation zu VERMEIDEN! Ethereum würde noch mehr gefährdet sein als klassische Clearingstellen. 
* Was passiert im Falle des Ausfalls (fehlerhafte Nachschussaufforderung)? Reputationssystem? 
* viele Verträge benötigen einen externen Marktkurs (z.B. Goldpreis von CME oder LIBOR, kinssätze). Wie können sie damit gefüttert werden? 
* kann Geld von der Clearingstelle zurückgebucht werden (z.B. überschüssige Mittel / Gewinne)? Es wäre einfacher, wenn dies nicht der Fall wäre. 
* Was ist, wenn die Parteien wirklich den Vertragsgegenstand austauschen wollen? Z.B.  keine Bargeld- / Ether- Abmachungen? (NICHT SUPPORTED VOM CLEARINGHAUS) 
* Verluste / Gewinne werden durch durch die von der Clearingstelle zur Verfügung gestellten  Hebel von Natur aus vergrößert. AKZEPTABEL FUER ETHERUEM? HINWEIS: OBWOHL DIE VOLLSTÄDIGE SUMME NICHT VORAB HINTERLEGT WERDEN BRAUCHT
### Code reference sample
