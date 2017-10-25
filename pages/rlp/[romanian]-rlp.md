---
name: RLP
category: 
---

Scopul RLP este acela de a coda matrici grupate arbitrar de date binare, si RLP este principala metoda folosita pentru a serializa obiecte in Ethereum. Singurul scop al RLP este acela de a coda structuri, codarea unor tipuri specifice de date atomice (strings, ints, float) sunt lasate pentru alte protocoale de ordin superior; in Ethereum standardul este reprezentarea numerelor intregi in forma endiana binara. Daca cineva doreste sa utilizeze RLP pentru a coda un dictionar, cele doua forme canonice sugerate vor folosi `[[k1,v1],[k2,v2]...]` keys in ordine lexicografica sau vor folosi codarea Patricia Tree, ca la Ethereum.

###Definitie

Functia de encodare RLP preia un element. Un element este definit dupa cum urmeaza:
-	Un sir (ex. o matrice octet) este un element
-	O lista de elemente este un element
De exemplu, un sir gol este un element, cum este sirul care conține cuvantul "pisica", o lista ce contine orice numar de siruri, ca si structuri de date mai complexe precum` ["pisica",["catelus","vaca"],"cal",[[]],"porc",[""],"oaie"]`. Retineti ca in contextul acestui articol, “sir” va fi folosit ca un sinonim pentru “un anumit numar de bytes de date binare”, nu sunt folosite codari speciale si nu implica resurse legate de continutul sirurilor.
Codarea RLP este definita dupa cum urmeaza:
-Pentru un singur byte a carui valoare se incadreaza in `[0x00, 0x7f]` , acel byte este propria sa codare RLP.
-Altfel, daca un sir are o lungime de 0-55 bytes, codarea RLP consta intr-un singur byte cu o valoare de 0x80 plus lungimea sirului, urmata de sir. Astfel, primul byte se incadreaza in intervalul `[0x80, 0xb7]`.
-Daca un sir este mai lung de 55 bytes, codarea RLP consta intr-un singur byte cu valoarea 0xb7 plus lungimea lungimii sirului in forma binara, urmata de lungimea sirului,urmata de sir. De exemplu, un sir de o lungime 1024 ar fi codat \xb9\x04\x00 , urmat de sir. Intervalul in care se incadreaza primul byte este, deci, `[0xb8, 0xbf]`.

- Daca sarcina utila a unei liste (payload) (ex. Suma lungimilor elementelor sale) are o lungime de 0-55 bytes, codarea RLP consta intr-un singur byte cu valoarea de 0xc0 plus lungimea listei urmata de concatenarea/juxtapunerea codarii RLP a elementelor. Intervalul in care se incadreaza primul byte este `[0xc0, 0xf7]`.
- Daca sarcina utila a unei liste (payload) este mai mare de 55 bytes, codarea RLP consta intr-un singur byte cu valoarea de 0xf7 plus lungimea lungimii listei in forma binara, urmata de lungimea listei , urmata de concatenarea/juxtapunerea codarii RLP a elementelor. Intervalul in care este plasat primul octet este `[0xf8, 0xff]`.

Codul, arata astfel:
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

###Exemple:

Sirul “dog”= `[ 0x83, 'd', 'o', 'g' ]`

Lista [ "cat", "dog" ] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`

Sirul gol ('null') = `[ 0x80 ]`

Lista goala =`[ 0xc0 ]`

Numarul intreg 15= `[ 0x0f ]`

Numarul intreg 1024 =`[ 0x82, 0x04, 0x00 ]`

Reprezentarea teoretica a setului de 2 , [ [], [[]], [ [], [[]] ] ] = `[ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`

Sirul "Lorem ipsum dolor sit amet, consectetur adipisicing elit" = `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`
