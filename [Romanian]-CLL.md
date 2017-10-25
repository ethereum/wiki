---
name: CLL
category: 
---

ECLL: Limbaj de programare ethereum, ce seamana cu C++.

Scopul ECLL este de a furniza un limbaj simplu si accesibil utilizatorilor, folosit pentru a scrie contracte, dar in acelasi timp poate compila usor si eficient script-ul Ethereum. ECLL indeparteaza concepte precum indici, accesaul direct la memorie si favorizarea unei sintaxe traditionale de conditionale, bucle, matrice si variabile. Suportul pentru funcii si suportul de prima clasa pentru functii vor fi atribuite unei viitoare versiuni a acestui limbaj, denumita EHLL ("Ethereum High Level Language"), mai puternica dar mai putin eficienta.

Specificarea va avea loc in trei parti. Prima parte va defini limbajul ca un arbore de sintaxa abstract, formalizat folosind o sintaxa S-expression, in stilul LISP; a doua parte va oferi specificatiile fiecarei operatiuni efectuate in parte, si a treia va arata cum se poate transpune AST in text.



###Sintaxa arborescenta

O expresie se defineste astfel:
- un numar e o expresie
- un nume variabil e o expresie
- `("arr" a b) `este o expresie, unde `a` si `b` sunt expresii. Aceasta este echivalentul in C `a[b]`.
- `(OP a b)` e o expresie, unde `OP` se afla in setul `(+ - * / % s/ s% ^ < > <= >= ==)`
-  `(OP a)` e o expresie, unde `a` se afla in setul `(- !) `
- Valorile speciale `tx.datan`, `tx.value`, `tx.sender`, `contract.address`, `block.number`, `block.difficulty`, `block.timestamp`, `block.parenthash` and `block.basefee` sunt expresii
- `("pseudo" a b)` este o expresie, unde `a` este o pseudomatrice and `b` este o expresie
- `("fun" a b...)` este o expresie, unde `a` este  o returning function and `b...` contine numarul valid de argumente.

O pseudofunctie se defineste astfel:
- `block.contract_storage` este o pseudofunctie

Un pseudoarray se defineste astfel:
- `tx.data` si `contract.storage` sunt pseudomatrici
- `("pfun" a b...)` unde `a` este o pseudofunctie si `b...` contine numarul valid de argumente este o pseudomultime.
`contract.storage` este o pseudomatrice mutabila, si celelalte doua sunt pseudomatrici immutabile.

O returning function se defineste astfel:
- `block.account_balance`, `sha256`, `sha3`, `ripemd160`, `ecvalid` sunt returning functions
- `array` este o returning function

O multireturning function se defineste astfel:
- `ecsign` and `ecrecover` sunt multireturning functions

O multiexpresie se defineste astfel:
- `("mfun" a b...)` unde a este o multireturning function si `b...` contine numarul valid de argumente este o multiexpresie

O acting function este definita dupa cum urmeaza:
`mktx` este o acting function
O expresie left-hand-side se defineste astfel:
- o variabila este o expresie left-hand-side.
- `("arr" a b)` este o expresie left-hand-side, unde `a` este o expresie left-hand-side si `b` este o expresie
-  `("pseudo" a b)` este o expresie left-hand-side, unde `a` este o pseudomatrice mutabila si `b` este o expresie.

O afirmatie se defineste astfel:
- `("afun" a b...)` unde `a` este o acting function si `b...` contine numarul valid de argumente si o afirmatie
- `("set" a b)` unde `a` este o expresie left-hand-side si `b` este o expresie si o afirmatie
- `("mset" a... b)` unde `a...` este o lista de expresii left-hand-side cu numarul valid de argumente si `b` este o multiexpression si o afirmatie.
- `("seq" a...)` unde `a...` reprezinta afirmatii
- `("if" a b)` unde `a` este o expresie `b` e o afirmatie
- `("if" a1 b1 "elif" a2 b2 "elif" a3 b3 ...)` unde `ak` sunt expresii and `bk` sunt afirmatii
- `("if" a1 b1 "elif" a2 b2 "elif" a3 b3 ... "else" c)` unde `ak` sunt expresii iar `bk` si `c` sunt afirmatii
- `("while" a b)` unde `a` este o expresie si `b` este o afirmatie
- `"exit"`.

###Definitii:

Pre-compilarea, daca sunt `n` variabile, fiecarei variabile ii va fi repartizata un index in `[0,n-1]`. Fie `ind(x)` indexul variabilei `x`, si fie  `M[x]` contractul de memorie la indexul `x`
In contextul unei expresii right-hand-side: 
Aritmetica functioneaza in modul evident. `(* (+ 3 5) (+ 4 8))`, de exemplu, este returnat ca 96.

•	`a && b` este o prescurtare pentru `!(!(a) + !(b))`

•	`a || b` este o prescurtare pentru `!(!(a + b))`
•	O variabila `x` trebuie sa devina `M[ind(x)]`
•	`("arr" a b)` returneaza `M[M[ind(a)]+b]`
•	`tx.datan` returneaza numarul de campuri de date din tranzactie. 
•	`("pseudo" "tx.data" i)` returneaza campul de date ith in tranzactie sau zero daca `i >= tx.datan`
•	`tx.sender`, `tx.value`, `block.basefee`, `block.difficulty`, `block.timestamp`, `block.parenthash` and `contract.address` returneaza valorile evidente. 
•	`("pseudo "contract.storage" i)`  returneaza stocarea contractului la index `i`.
•	`("fun" "block.account_balance" a)` returneaza balanta adresei `a`
•	`("pseudo" ("pfun" "block.contract_storage" a) b)` returneaza stocarea contractului apartinand contului `a` la indexul `b`
•	`("mfun" "ecsign" hash key)` retuneaza ternarul `v,r,s` din semnatura
•	`("mfun" "ecrcover" h v r s)` returneaza cheia publica `x,y`
•	`("mfun" "sha256" len arr)` returneaza hash-ul SHA256 al sirului de lungimea len incepand de la  `M[ind(arr)]`. La fel si `sha3` si `ripemd160work`.
•	`("fun" "array")` returneaza `2^160 + M[2^256 - 1]` si seteaza `M[2^256 - 1] <- 2^160 + M[2^256 - 1]`
In contextul unei expresii left-hand-side:
•	O variabila `x` returneaza `ind(x)`
•	`("arr" a b)` returneaza `a + b` unde `a` si `b` au fost deja evaluate
•	`("pseudo" a b)` returneaza un obiectcare, daca este setat, modifica valoarea pseudomatricei referente la indexul `a` la `b` in loc sa faca un memory set.
In contextul unei afirmatii: 
•	`("afun" "mktx" dest value dn ds)` creeaza o tranzactie trimitand `value` ether la `dest` cu campurile de date `dn` incepand de la `M[ind(ds)]`
•	`("set" a b) sets M[a] <- b` unde `a` si `b` au fost deja evaluate
•	`("mset" a... b)` seteaza secvential indexul de memorie la fiecare din valorile `a...` la o valoare din multiexpresia `b`
•	`("if" a b), ("while" a b)` si derivatele vor functiona asa cum functioneaza si in alte limbaje.
###Sintaxa
•	Din punct de vedere aritmetic se realizeaza folosind notatii infixe folosind C++ operator cu prioritate: `^` then `* / % s/ s%` then `+ -` then `< <= > >=` then `==` then `&&` then `||`.Adica, formulele aritmetice se vor comporta cum se comporta in mod normal; de exemplu, `(+ (* 3 5) (* x y))` ar fi `(3 * 5 + x * y)` si `(* (+ 3 5) (+ x y))` ar fi `((3 + 5) * (x + y))`
•	`("arr" a b)` si `("pseudo" a b)` vor fi reprezentate de `a[b]`
•	`("fun" a b...)`, `("mfun" a b...)`, `("afun" a b...)` si `("pfun" a...)` vor fi toate reprezentate ca `a(b1,b2, ... ,bn)`
•	Multiexpresiile vor fi reprezentate ca `a1, a2, ... an`
•	`("set" a b)` si `("mset a... b)` vor fi reprezentate ca `a = b` si respectiv `a1, a2 ...` ,`an = b`.
•	`("seq" a...)` consta in fiecare din afirmatiile de pe propria linie separata
•	`("if" a b)` si `("while" a b)` vor fi reprezentate ca in Python, folosind keyword-ul direct urmat de expresia pentru `a` si afirmatia indented pentru `b`
•	Valorile pot fi reprezentate fie ca numere intregi sau in string quotes, in care caz vor fi tratate ca hexadecimale.
•	`exit` –se iese.

###Exemple:

Factorial:
```python
 x = 1
 n = 1
 while x < 10:
     n = n * x
     x = x + 1
```
Secventa Fibonacci:
```python
 a = array()
 a[0] = 1
 a[1] = 1
 i = 2
 while i < 70:
     a[i] = a[i-1] + a[i-2]
     i = i + 1
 mktx("0676d13c8d2cf5e9e988cc3b5f6dfb5a6a3938fa",a[69],70,a)
```
Un exemplu real – un contract simplu de expediere pentru a crea un cont transferabil: 
```python
 if tx.value < tx.basefee * 200:
     exit
 else:
     a = contract.storage[2^256 - 1]
     if !(tx.sender == a) && (tx.sender > 0):
         exit
 if tx.data[0] == 2^160:
     contract.storage[2^256 - 1] = tx.data[1]
 else:
     if a == 0:
         contract.storage[2^256 - 1] = tx.sender
     o = array()
     i = 0
     while i < tx.datan - 1:
         o[i] = tx.data[i+1]
         i = i + 1
     mktx(tx.data[0],tx.value - tx.basefee * 250,i,o)
```
###Sintaxa alternativa: C++ style

•	`("seq" a...)` este implementat prin separarea afirmatiilor prin semicoloane. Randurile libere sunt ignorate. 
•	`("if" a b)` si `("while" a b)` vor fi reprezentate ca in C++, stocand `a` intre paranteze si `b` intre acolade.

###Sintaxa alternativa: Lisp style

AST  este utilizat ca si cod direct , cu modificarea minora urmatoare:`("fun" a b...)`, `("pfun" a b...)` si `("mfun" a b...)` sunt inlocuite cu `(a b...)`.
