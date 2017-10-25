---
name: RLP
category: 
---

Der Zweck von RLP ist es, beliebig verschachtelte Arrays von binären Daten zu kodieren. RLP wird als Hauptcodierungsverfahren verwendet, um Objekte in Ethereum in serieller Reihenfolge zu kodieren. Das heisst RLP kodiert Strukturen, spezielle Datentypen wie [Strings] (http://de.wikipedia.org/wiki/Zeichenkette), [Integers] (http://de.wikipedia.org/wiki/Integer_%28Datentyp%29) und [Floats] (http://de.wikipedia.org/wiki/Gleitkommazahl), bis hin zu höheren Protokollebenen. Integer werden in Ethereum standardmäßig im [Big Endian] (http://de.wikipedia.org/wiki/Byte-Reihenfolge) Binärfomat verarbeitet.
Wenn man RLP z.B. zum Kodieren eines Wörterbuchs verwendet, sind die beiden vorgeschalgenen Standardformen entweder `[[k1, v1], [k2, v2] ...]` mit Schlüsselwörtern in lexikographische Ordnung oder besser die Kodierung des [Patricia Baums] (https://github.com/ethereum/wiki/wiki/Patricia-Tree) (wie es Ethereum tut).

### Definition
Die RLP-Koding erfolgt in einem Element. Ein Element wird wie folgt definiert:

* Ein String (z.B. eine Byte Array) ist ein Element
* Eine Liste von Elementen ist ein Elemet

Elemente können z.B. sein:
- ein leerer String
- ein String mit dem Wort "cat"
- eine Liste von beliebig vielen anderen Zeichen
- komplexere Daten wie  `["cat",["puppy","cow"],"horse",[[]],"pig",[""],"sheep"]`.
 
Bitte beachten: In dem Rest dieses Artikels wird "String" als Synonym für "eine bestimmte Anzahl von Bytes von binären Daten" verwendet. Es wird keine besondere Kodierung betrachtet und auch nichts über den Inhalt des Strings ausgesagt.

Die RLP-Kodierung funktioniert wie folgt:
* Für ein einzelnes Byte innerhalb des Bereiches `[0x00, 0x7f]` erfolgt eine individuelle RLP-Kodierung.
* Ist ein String 0 bis 55 Byte lang enthält die RLP-Kodierung den Wert **0x80** + die Länge des Strings, gefolgt vom Stringinhalt. Der Wertebereich des ersten Bytes ist `[0x80, 0xb7]`.
* Ist ein String länger als 55 Byte, enthält die RLP-Kodierung das erste Byte mit dem Wert **0xb7** + die Länge für String+Stringlänge in Binärformat, gefolgt von der Länge des Strings, gefolgt vom Stringinhalt. Z.B. ein 1024 Byte langer String wird kodiert als `\xb9\x04\x00` gefolgt vom Stringinhalt. Der Wertebereich des ersten Bytes ist `[0xb8, 0xbf]`.
* Ist die Gesamtlänge der Nutzdaten einer Liste (z.B. die kombinierte Länge aller Elemente)  zwischen 0 und 55 Bytes, enthält die RLP-Kodierung das Byte mit dem Inhalt **0xc0** + die Länge der Liste, gefolgt von der Verkettung der RLP-Kodierungen der Elemente. Der Wertebereich des ersten Bytes ist `[0xc0, 0xf7]`.
* Ist die Gesamtlänge der Nutzdaten einer Liste mehr als 55 Byte, enthält die RLP-Kodierung das Byte mit dem Inhalt **0xf7** +  die Länge für Liste+Listenlänge in Binärformat, gefolgt von der Länge der Liste, gefolgt von der Verkettung der RLP-Kodierungen der Elemente. Der Wertebereich des ersten Bytes ist `[0xf8, 0xff]`.
Als Code sieht das so aus:
```python
def rlp_encode(input):
    if isinstance(input,str):
        if len(input) == 1 and chr(input) < 128: return input
        else: return encode_length(len(input),128) + input
    elif isinstance(input,list):
        output = encode_length(len(input),192)
        for item in input: output += rlp_encode(item)
        return output
def encode_length(L,offset):
    if L < 56:
         return chr(L + offset)
    elif L < 256**8:
         BL = to_binary(L)
         return chr(len(BL) + offset + 55) + BL
    else:
         raise Exception("input too long")
def to_binary(x):
    return '' if x == 0 else to_binary(int(x / 256)) + chr(x % 256)
```
### Beispiele

Der String "dog" = [ 0x83, 'd', 'o', 'g' ]

Die Liste [ "cat", "dog" ] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`

Der leere String ('null') = `[ 0x80 ]`

Die leere Liste = `[ 0xc0 ]`

Der Integer 15 = `[ 0x0f ]`

Der Integer 1024 = `[ 0x82, 0x04, 0x00 ]`

Die [mengentheoretischen Darstellung] (http://en.wikipedia.org/wiki/Set-theoretic_definition_of_natural_numbers) von zwei, `[ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`

Der String "Lorem ipsum dolor sit amet, consectetur adipisicing elit" = `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`
