---
name: Patricia Tree
category: 
---

###Specificatiile Arborelui Merkle Patricia

Arborele Merkle Patricia furnizeaza o structura autentificata cryptografic de date care poate fi folosita pentru stocarea tuturor legaturilor (key, valori), desi pentru scopul acestui document restrictionam valorile si keys la siruri (pentru a inlatura aceasta restrictie, folositi orice format de serializare pentru alte tipuri de date). Ele sunt complet deterministe, insemnand ca un arbore Patricia cu aceeasi legatura (valoare, key) va fi exact la fel pana la ultimul byte si astfel va avea aceeasi radacina hash, va furniza the holy grail al eficientei O(log(n)) pentru insertii, cautari si stergeri, si sunt mult mai usor de inteles si de codat  decat alte alternative mai complexe bazate pe comparatii cum ar fi arborii rosu-negru.

###PREAMBUL : Arborii Radix de baza

Intr-un arbore de baza radix, fiecare nod arata astfel:

    [ value, i0, i1 ... in]

Unde i0… in reprezentarea valorii simbolurilor alfabetului (deseori binare sau hex) este valoarea terminala a nodului, si valorile din i0… in sloturi sunt fie NULL fie indicatoare (in cazul  nostr hash-uri) pentru alte noduri.  Aceasta formeaza un stoc de baza (key, valoare), de exemplu, daca esti interesat de valoarea din arbore a obiectului “dog”, intai va trebui efectuata convertirea in alfabet ( rezultand 646f67 daca folosim hex), iar apoi de coboara pe traiectoria arborelui pana la sfarsitul ei, unde se poate citi valoarea.  Adica, intai se cauta hash-ul radacina in stocarea key/value pentru a ajunge la nodul radacina, dupa care se cauta nodul 6 al nodului radacina, pentru a cobori acel nod cu un nivel, apoi se cauta nodul 4 al acestui rezultat, urmand nodul 6, si tot asa, mergand pe urmatoarul traseu root ->6->4->6->f->6->7,  iar la sfarsit se cauta valoarea nodului obtinut si se returneaza rezultatul.

Update-urile si operatiile de stergere pentru arborele radix sunt simple, si pot fi definite mai pe scurt dupa cum urmeaza:

```
def update(node,key,value):
    if key == '':
        curnode = db.get(node) if node else [ NULL ] * 17
        newnode = curnode.copy()
        newnode['value'] = value
    else:
        curnode = db.get(node) if node else [ NULL ] * 17
        newnode = curnode.copy()
        newindex = update(curnode[key[0]],key[1:],value)
        newnode[key[0]] = newindex
    db.put(hash(newnode),newnode)
    return hash(newnode)

def delete(node,key):
    if key == '' or node is NULL:
        return NULL
    else:
        curnode = db.get(node)
        newnode = curnode.copy()
        newindex = delete(curnode[key[0]],key[1:])
        newnode[key[0]] = newindex
        if len(filter(x -&gt; x is not NULL, newnode)) == 0:
            return NULL
        else:
            db.put(hash(newnode),newnode)
            return hash(newnode)
```

Partea “Merkle” din arborele  radix provine din faptul ca hash-ul cryptografic deterministic al unui nod este utilizat ca indicator spre nod, si nu dintr-o locatie de memorie 32-bit sau 64-bit  cum s-ar putea intampla intr-un arbore mai traditional implementat in C. Acesta furnizeaza o forma de autentificare cryptografica  la structura datelor. Daca radacina hash a unui trie este cunoscuta public, atunci oricine poate furniza  o dovada a unei (key/valori) perechi care nu exista din moment ce hash-ul radacina este bazat pe toate hash-urile de sub el, deci orice modificari ar schimba radacina hash.

Cu toate acestea, arborii radix au o limitare majora: ineficienta. In cazul in care se doreste stocarea doar unei legaturi (key/valoare) unde key are o lungime de cateva sute de caractere, va fi necesar un KB de spatiu extra pentru a stoca cate un pivel per caracter, iar fiecare cautare sau stergere va insemna sute de pasi. Arborele Patricia rezolva aceasta problema.

###Specificatie: Codarea compacta a secventei hex cu terminator optional

Modul compact traditional de a coda un sir hex este de a-l converti in binar , adica un sir ca 0f1248 ar deveni 3 bytes 15,18,72. Cu toate acestea, aceasta abordare are o mica problema: daca lungimea sirului este impara? In acest caz, nu exista nici o modalitate de a distinge sirurile 0f1248 si f1248. In plus, aplicatia noastra in Arborele Merkle Patricia necesita  trasatura aditionala cu care un sir hex poate avea un “simbol special terminator” la sfarsit (denotat de ‘T’). Un simbol terminator poate aparea o singura data, si doar la sfarsitul unui sir. Un mod alternativ de a privi lucrurile  ar fi sa nu ne gandim la existenta unui simbol terminator, ""but instead treat bit specifying the existence of the terminator symbol as a bit specifying that the given node encodes a final node, where the value is an actual value, rather than the hash of yet another node.""
 
Pentru a rezolva ambele probleme, fortam primul nibble al bytestreamului final pentru a coda doua flags, specificand ca lungimea e impara (ignorand simbolul T) si statusul terminator, acestea sunt plasate, respectiv, in cei mai de jos doi biti ai primului nibble.In eventualitatea in care un sir hex este par, trebuie introdus un al doile nibble (cu valoarea zero) pentru a asigura ca sirul hex este par in lungime si astfel este reprezentabil  prin un numar intreg de bytes.  Astfel putem construi urmatoarea codare:

```
def compact_encode(hexarray):
    term = 1 if hexarray[-1] == 16 else 0
    if term: hexarray = hexarray[:-1]
    oddlen = len(hexarray) % 2
    flags = 2 * term + oddlen
    if oddlen:
        hexarray = [flags] + hexarray
    else:
        hexarray = [flags] + [0] + hexarray
    // hexarray now has an even length whose first nibble is the flags.
    o = ''
    for i in range(0,len(hexarray),2):
        o += chr(16 * hexarray[i] + hexarray[i+1])
    return o
```
Exemple:
```
&gt; [ 1, 2, 3, 4, 5 ]
'\x11\x23\x45'
&gt; [ 0, 1, 2, 3, 4, 5 ]
'\x00\x01\x23\x45'
&gt; [ 0, 15, 1, 12, 11, 8, T ]
'\x20\x0f\x1c\xb8'
&gt; [ 15, 1, 12, 11, 8, T ]
'\x3f\x1c\xb8'
```
###Principalele specificatii: Arborele Merkle Patricia

Arborele Merkle Patricia rezolva problemele prezentate mai sus prin adaugarea  unei extra complexitati  structurii datelor. Un nod intr-un arbore Merkle Patricia este unul dintre urmatoarele:

1.	NULL (reprezentat ca un sir gol)
2.	O multime cu doua elemente [key.valoare]
3.	O multime de 17 elemente [v0…v15, vt]

Ideea este aceea ca in eventualitatea in care exista o ruta lunga de noduri fiecare cu un singur element, facem un shortcut la un descent  prin setarea unui nod [key/valoare], unde key coboara traiectoria hexadecimala, in codarea compacta descrisa mai sus, si valoarea este doar hash-ul nodului, la fel ca in arborele radix standard. Deasemenea, mai adaugam o schimbare conceptuala:  nodurile interne nu mai au valori, only leaves with no children of their own can, cu toate acestea, pentru a fi generici vrem ca stocarea valoarea/key sa poata stoca keys cum ar fi “dog”, “doge” in acelasi timp, doar adaugam un simbol terminator (16) la alfabet astfel incat sa nu existe niciodata o valoare “en-route” pentru o alta valoare. Unde un nod are o referinta in alt nod, care este inclus H(rlp.encode(x)) unde H(x) = sha3(x) if len(x) >= 32  altfel x si codarea rlp este functia codarii RLP. Retineti ca in momentul updatarii unui trie, va trebui stocata perechea key/valoare (sha3(x), x) intr-un tabel de cautare persistenta cand se creeaza un nod cu lungimea >=32, dar daca nodul este mai scurt de atat nu este necesara cand lungimea <32 pentru ca functia f(x)=x este reversibila.

Mai jos este codul extins pentru a obtine un nod intr-un arbore Merkle Patricia:
def get_helper(node,key):
```
    if key == []: return node
    if node = '': return ''
    curnode = rlp.decode(node if len(node) < 32 else db.get(node))
    if len(curnode) == 2:
        (k2, v2) = curnode
        k2 = compact_decode(k2)
        if k2 == key[:len(k2)]:
            return get(v2, key[len(k2):])
        else:
            return ''
    elif len(curnode) == 17:
        return get_helper(curnode[key[0]],key[1:])

def get(node,key):
    key2 = []
    for i in range(len(key)):
        key2.push(int(ord(key) / 16))
        key2.push(ord(key) % 16)
    key2.push(16)
    return get_helper(node,key2)
```
Exemplu: presupunem ca avem un arbore care contine perechile ('dog', 'puppy'), ('horse', 'stallion'), ('do', 'verb'), ('doge', 'coin'). In primul rand convertim keys in format hex:
```
[ 6, 4, 6, 15, 16 ] : 'verb'
[ 6, 4, 6, 15, 6, 7, 16 ] : 'puppy'
[ 6, 4, 6, 15, 6, 7, 6, 5, 16 ] : 'coin'
[ 6, 8, 6, 15, 7, 2, 7, 3, 6, 5, 16 ] : 'stallion'
```
Acum alcatuim arborele:
```
ROOT: [ '\x16', A ]
A: [ '', '', '', '', B, '', '', '', C, '', '', '', '', '', '', '', '' ]
B: [ '\x00\x6f', D ]
D: [ '', '', '', '', '', '', E, '', '', '', '', '', '', '', '', '', 'verb' ]
E: [ '\x17', F ]
F: [ '', '', '', '', '', '', G, '', '', '', '', '', '', '', '', '', 'puppy' ]
G: [ '\x35', 'coin' ]
C: [ '\x20\x6f\x72\x73\x65', 'stallion' ]
```