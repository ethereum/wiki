---
name: Block Protocol 2.0
category: 
---

###Block Protocol 2.0

Una din principalele critici aduse Ethereum-ului, si in general protocoalelor blockchain de tipul Bitcoin, este  problema scalabilitatii. Desi dezvoltatorii de baza ai Bitcoin, si ai altor platforme , cum ar fi Ripple, au continuat sa faca imbunatatiri iterative modului in care blockchain-ul este stocat, incluzand inovatii, cum ar fi separarea de starea (“registrul” sau “setul UTXO”) din lista de tranzactie sau folosind structuri separate de date pentru a stoca cele două  pe disc / memorie, nimeni nu a implementat cu succes nici o modalitate de a îmbunătăți limitarea fundamentala pe care fiecare nod complet trebuie să prelucreze fiecare tranzacție. In acest punct, nici Ethereum nu rezolva aceasta problema. Totusi, protocolul block descris aici, si in special mecanismul stack trace, permite sexistenta “light nodes” sigure, care downloadeaza doar header-ele fiecarui block si nu intregul set de tranzactii, mentinand acelasi nivel de securitate pentru utilizatorii sai ca si nodurile intregi, sub asumptia gresita ca cel putin un nod cu putere de minare  non-neglijabila( ie. >0.01%) sau ether din sistem este onesta.
Data definitions
Un block intreg este stocat ca:
[
    block_header,
    transaction_list,
    uncle_list,
    stack_trace
]

Unde:
transaction_list = [
    transaction 0,
    transaction 1,
    ...
]

uncle list = [
    uncle_block_header_1,
    uncle_block_header_2,
    ...
]

block_header = [
    parent hash,
    number,
    TRIEHASH(transaction_list),
    TRIEHASH(uncle_list),
    TRIEHASH(stack_trace),
    coinbase address,
    state_root,
    difficulty,
    timestamp,
    extra_data,
    nonce
]

stack_trace = [
    [ medhash 0, stkhash 0 ],
    [ medhash 1, stkhash 1 ],
    ...
]

Fiecare tranzactie si header al block-ului  unchi este in sine furnizat direct sub forma unei liste, si nu intr-o forma serializata. uncle_list si transaction_list sunt listele header-lor block-ului unchi si ale tranzactiilor din block. Retineti ca numarul block, dificultatea si timestamp sunt numere intregi , si prin urmare nu pot incepe cu zero; extra_data si  nonce  pot fi multimi de cel mult 32 bytea, NU liste, desi aceasta verificare in special nu ar trebui facuta pentru block-ul geneza unde campul extra_data va ocupa multi kilobytes. 
 state_root este radacina  Arborelui Patricia  continand perechi (key/value) pentru toate conturile, unde fiecare adresa este reprezentata ca un sir binar 20-byte. La adresa fiecarui cont, valoarea stocata in arborele Merkle Patricia este un sir  care este forma RLP serializata a unui obiect de forma: 
[ balance, nonce, contract_root ]

nonce  este numarul de tranzactii facute din cont, si este incrementat de fiecare data cand este facuta o tranzactie. Scopul este (1) de a face fiecare tranzactie valida pentru a preveni atacurile reply, si (2) de a face imposibil (mai precis, criptografic nefezabil) construirea unui contract cu acelasi hash ca al unui contract deja existent. balance  se refera la balanta contului , denominata in wei. contract_root este radacina unui alt arbore Patricia, continand memoria contractului, daca acel cont este controlat de un contract. Daca un cont nu este controlat de un contract, radacina contractului va fi doar un sir gol.
Pentru a intelege toate cele de mai sus, avem nevoie si de cateva definitii ale functiilor:
def H(x):
    if len(rlp.encode(x)) < 32: return x
    else: return sha3(rlp.encode(x))

def H20(x):
    if len(rlp.encode(x)) < 20: return x
    else: return sha3(rlp.encode(x))[:12]

def adjust_difficulty(pdiff,ptime,ntime):
    if ntime > ptime + 42: return pdiff - int(pdiff / 1024)
    else: return pdiff + int(pdiff / 1024)

def int_to_bin(n,bytes):
    return '' if bytes == 0 else int_to_bin(int(n / 256),bytes - 1) + chr(n % 256)

def TRIE(objs):
    t = Trie()
    for i in range(len(objs)):
        t.insert(int_to_bin(i,32),rlp.encode(objs[i]))
    return t

def TRIEHASH(objs):
    return TRIE(objs).root

Deasemenea, dorim cateva metode pentru a ne ocupa de tries ca stack-uri
def TRIELEN(trie):
    i = 0
    while trie.get(int_to_bin(i,32)): i += 1
    return i

def TRIETOP(trie):
    return trie.get(int_to_bin(TRIELEN(trie)-1,32))

def TRIEPOP(trie):
    trie.update(int_to_bin(TRIELEN(trie)-1,32),'')

def TRIEPUSH(trie,node):
    trie.update(int_to_bin(TRIELEN(trie),3),node)

Functia de minare este tentativa si va fi inlocuita odata ce stim ca avem alternative mai bune:
def compute_valid_nonce(header):
    header[10] = 0
    while not verify_pow(header):
        header[10] += 1
    return header[10]

def verify_pow(header):
    return sha3(sha3(header[:10]) + header[10]) * header[7] <= 2**256

Procesul de minare
 
Cand se mineaza un block, minerul trece prin urmatorul proces:
1-	Take as inputs
•	uncle_headers – pentru a fi  lista de header-e cunoscute nefolosite ale unchilor 
•	timestamp  pentru a fi  timestamp-ul curent 
•	parent pentru a fi block-ul parinte 
•	extra_data pentru ca  extra data dorita sa fie adaugata block-ului 
•	coinbase pentru a fi adresa coinbase dorita 
•	txlist pentru a fi  lista de tranzactii ce urmeaza a fi adaugata
2- Set:
•	difficulty = adjust_difficulty(parent.difficulty,timestamp,parent.timestamp)
•	reward = 15 * 10^18 (provizoriu)
•	block_header = [ parent.hash, parent.number + 1, TRIEHASH(txlist), TRIEHASH(uncle_headers), 0, coinbase, 0, difficulty, timestamp, extra_data, 0 ]
3- Initializare:
•	state pentru a fi starea de parinte block 
•	txstack = TRIE(txlist)
•	stacktrace = TRIE([])
4- In timp ce  stacktrace.root != '':
•	TRIEPUSH(stacktrace,[state.root,txstack.root])
•	Se aplica tranzactia TRIETOP(txstack) la state.
•	TRIEPOP(txstack)
•	Fie  L[0] ... L[m-1]  lista de tranzactii noi rezultata de acea tranzactie via MKTX, in ordinea in care au fost produse in timpul executie script.
•	Initializati j = m-1. While j >= 0, TRIEPUSH(txstack,L[j]) si j -= 1
5- Faceti urmatoarele modificari arborelui state (de stare):
•	Cresteti balanta coinbase prin reward
•	Pentru fiecare unchi  u in uncle_headers, cresteti balanta lui u.coinbase prin reward * 13/16 si cresteti balanta lui coinbase prin reward * 1/16
6- Completati primele doua zerouri in block_header cu  state.root si TRIEHASH(stack_trace)
7- Setati nonce = compute_valid_nonce(block_header), si completati zeroul ramas in header-ul block cu valoarea lui 

Block Validation Algorithm 
1- Luati ca inputs :
•	block pentru a fi header-ul block 
•	uncle_list pentru a fi lista unchiului block 
•	transaction_list pentru a fi lista tranzactiei block 
•	stacktrace pentru a fi stack trace a block-ului 
•	now pentru a fi timpul actual precum e masurat de CPU-ul minerului 
2- Verificati urmatoarele:
•	Exista un obiect, care este un block, in baza de date cu block.prevhash ca fiind hash-ul sau? Fie  parent acel block.
•	Este valid proof of work-ul block-ului?
•	Este valid proof of work-ul tuturor header-lor unchi?
•	 Sunt toti unchii unici si unchi intr-adevar? (ie. Copilul parintelui parintelui, dar nu parintele)?
•	Este block.timestamp <= now + 900 and is block.timestamp >= parent.timestamp?
•	Este block.number == parent.number + 1?
•	Este  block.difficulty == adjust_difficulty(parent.difficulty,timestamp,parent.timestamp)?
•	Este  block.transaction_hash = TRIEHASH(transaction_list)?
•	Este  block.stacktrace_hash = TRIEHASH(stacktrace)?
•	Este  block.uncle_hash = H(uncle_list)?
3- Initializare:
•	state  pentru a fi starea block a block-ului parinte 
•	txstack = TRIE(transaction_list)
•	i = 0
4- Fie  stacktrace[k] = [ M[k], H[k] ], defaulting to '' if k is out of bounds. While i < len(stacktrace):
•	Verificati daca state.root == M[i] and txstack.root == H[i]
•	Aplicati  transaction_list[i] la  state.
•	Aplicati tranzactia  TRIETOP(txstack) la  state.
•	TRIEPOP(txstack)
•	Fie  L[0] ... L[m-1] lista de noi tranzactii rezultate de acea tranzactie via MKTX, in ordinea in care au fost produse in timpul executiei scriptului.
•	Initializati j = m-1. While j >= 0, TRIEPUSH(txstack,L[j]) and j -= 1
•	Setati  i += 1
5- Faceti urmatoarele modificari la  state:
•	Cresteti balanta coinbase prin reward
•	Pentru fiecaree unchi  u in uncle_headers, cresteti balanta de u.coinbase prin  reward * 13/16 si cresteti balanta de coinbase prin reward * 1/16
6- Verificati daca txstack.root == ''
7- Verificati daca  state.root == block.state_root
8- Daca orice verificare a esuat, returnati FALSE. Altfel, returnati  TRUE.
Daca un block este valid, determinati TD(block) ("total difficulty") pentru noul block.. TD este definit recursiv prin TD(genesis_block) = 0 si TD(B) = TD(B.parent) + sum([u.difficulty for u in B.uncles]) + B.difficulty. Daca noul block are TD mai mare decat block-ul curent, setati block-ul curent la noi block-uri si continuati la urmatorul pas. In caz contrariu, iesiti. 
Semi-collaborative Block Validation Via Challenge-Response Protocol
In Ethereum, un "light node"poate fi definit ca un nod care accepta header-e block, si fac verificarea in (2) cu exceptia tranzactiei si verificarilor stacktrace trie hash dar nu fac verificarea in (4) si (6), similar cu verificarea headers-only pe care light nodes o fac in Bitcoin. Light nodes ar stoca, astfel,  state roots, si poate o parte a starii, insa nu in intregime. Daca un light node vrea sa stie balanta sau starea contractului unui cont oarecare, poate solicita valoarea altor noduri din retea , alaturi de subsetul minimal al nodurilor arborelui Patricia care dovedeste ca perechea key/value data este intr-adevar in stare.
Desi Ethereum nu poate functiona fara cel putin cateva noduri intregi care proceseaza si verifica fiecare tranzactie care ia loc in retea, acest block protocol este creat pentru a furniza o asigurare oarecum mai slaba: atat timp cat exista cel putin un nod intreg onest, clientii ligh (?) pot fi la fel de siguri si pot furniza aceleasi stimulente spre descentralizare precum nodurile intregi. Mecanismul se bazeaza pe un protocol provocare-raspuns in care un nod intreg poate, sub anumite conditii, sa prezinte o provocare precum o anumita parte din block este invalida, si ar depinde de miner furnizarea unui raspuns legat de ce noduri light pot fi verificate eficient. Daca o provocare ramane necontestata, atunci nodurile light s-ar indoi de acel block.
In validarea algoritmului descris in sectiunea anterioara, retineti ca exista cateva locuri specifice in care un block poate esua:
1. Una din verificarile in (2), separata de verificarea  hash-ului  trie al listei tranzactiei sau a hash-ului trie trace stack, esueaza.
2. Verificarea hash-ului trie al listei tranzactiei esueaza.
3. Verificarea hash-ului trie al trace stack esueaza
4.Una din verificarile din (4) esueaza pentru i=0
5. Una din verificarile din (4) reuseste pentru toate i<k dar esueaza pentru i=k
6.Verificarea din (6) sau (7) esueaza.
(1) este realizat automat de noduri light, dar celelalte sase necesita o cantitate mai mare de date pentru a se verifica corespunzator, si astfel clientii light nu vor putea detecta astfel de defecte ei insisi. Cu toate acestea, alte noduri vor putea, si vor putea lansa provocari la orice pas. Indeiferent de unde vine provocarea, daca block-ul este corect un alt nod va putea construi un proof of localized legitimacy. Setul de proofs of localized legitimacy posibil are acoperire 100%, in teorie, o colectie mare de proofs of localized legitimacy poate fi combinat intr-un proof complet precum ca un block este valid.
Pentru (2) si (3), pentru ca un trie hash check sa treaca mai departe trebuie indeplinite 2 conditii:
1. pentru toti indicii i<k pentru k, trebuie sa existe o tranzactie valida in trie la i.
2.pentru toti indicii i.k, totul trebuie sa fie blank.
O provocare de invaliditate consta in fie (1) un index i care indica o potentiala tranzactie invalida, fie (2) un index i care indica un spatiu gol si un index j>i care indica o tranzactie. Un raspuns trebuie sa contina (1) i, (2) j, dasca este aplicabil, (3) subsetul arborelui Patricia trebuie sa verifice valorile la i si j in trie.
Pentru (5), provocarea de invaliditate consta intr-un index k astfel incat tot pana la indexul k este valid, cu exceptia indexului k. Un raspuns consta in (1) k, (2) intrarile stack trace la k-1 si k impreuna cu subsetul arborelui Patricia pentru a le verifica, (3) un subset de noduri ale arborelui Patricia din state tree trebuie sa verifice calculul. Pentru (4), un raspuns consta doar intr-un subset de noduri ale arborelui Patricia pentru a dovedi intrarea stack trace la indexul 0, de unde doua componente pot fi verificate cu hash-ul listei tranzactiei in block header si parintele block state. Pentru (6), un raspuns functioneaza in acelasi mod ca si in (4), cu exceptia ca verificarea raspunsului necesita atat procesarea ultimei tranzactii cat si recompensarea block a unchilor si a coinbase.
Economics
Exista o imperfectiune in protocolul descris mai sus; este vulnerabil la negarea service attack. Mai exact pentru ca provocarile sunt mult mai usor de produs decat sunt raspunsurile de produs sau verificat, este posibil ca nodurile sa polueze reteaua cu provocari false, fortand nodurile light sa respinga orice  block atat timp cat alte noduri sunt incapabile sa tina pasul cu verificarea. Pentru a rezolva acest lucru, introducem o rezolvare simpla:O provocare trebuie sa fie semnata de un nod care fie (1) a minat unul din ultimele 10000 de block-uri , sau (2) care detine cel putin  0.01% din tot etherul. Nodurile light ar tine apoi scoruri de calitate pentru toti peers si ii pot downgrada daca acestia prezinta o provocare ce este contracarata cu succes. Pentru ca aceasta metoda nu este anonima, cererea de inregistrare a unei dovezi de proprietate legata de blockchain adresata nodurilor nu poate fi contracarata prin reconectarea sub o alta adresa IP sau alte mecanisme asemanatoare.
Protocolul poate fi descris intr-u totul ca fiind stimulent-compatibil dupa cum urmeaza:
1.	Minerii au stimulentul pentru a inregistra provocari block-urilor minate de alte noduri pentru ca daca o provocare este realizata cu succes si daca acestia mineaza block-ul urmator vor avea oportunitatea de a castiga o recompensa de 0.0625X prin includerea header-ului blocului invalid ca unchi.
2.	Minerul unui block, si toti unchii din acel block, au cel putin doar stimulentul de a raspunde provocarilor.
3.	Nodurile light au stimulentul de a respecta regulile de validitate ale block-ului, pentru ca toata lumea le respecta, un argument explicat mai pe larg in http://themonetaryfuture.blogspot.ca/2011/07/bitcoin-decentralization-and-nash.html. Fara protocolul provocare-raspuns, acest argument nu se aplica, din cauza costului determinarii daca un block este valid sau nu, care este prohibitiv de mare; cu protocolul , acest rationament nu se mai aplica.
4.	Partenerii subtantiali (stakeholders) au stimulentul de a promova integritatea perceputa a sistemului pentru a maximiza valoarea unitatilor de valuta detinute, si astfel pot dori sa ajute activ prin introducerea de provocari si raspunsuri acolo unde este posibil.
5.	Statistic vorbind, mult mai mult de 0,01% din agentii din inteeriorul agentilor economici din lumea reala, fie ca sunt motivati sau nu de puterea economica, tind sa fie motivati de considerente altruiste/ideologice.Cota de piata a organizatiilor de binefacere din intreaga lume este o dovada mai mult decat suficienta.

Comparatia cu Bitcoin
In Bitcoin, se poate crea un protocol provocare-raspuns pentru a atinge o functionalitate similara prin cateva principii similare, dar este un singur mod cheie in care un astfel de protocol in Bitcoin ar fi inadecvat: Taxele pentru mineri. Deoarece Bitcoin nu include un mecanism Arbore-Merkle pentru adaugarea taxelor de tranzactii, singurul mod prin care se poate demonstra ca un block are o anumita suma de taxe de tranzactii este prin procesarea fiecarei tranzactii. Mai mult de atat,in  Bitcoin taxele pentru tranzactii merg in totalitate la miner. Astfel, in Bitcoin un nod light nu are cum sa determine daca un anumit block este valid sau daca ii percepe creatorului sau taxe excesive, si se poate baza doar pe majoritatea calculata ca sursa de informatii. In Ethereum, toate schimbarile aduse starii sunt incorporate in stacktrace, astfel nu exista aceasta slabiciune si, avand in vedere ipoteza securitatii subrede ca cel putin un nod intreg cu minim 0.01% putere de minare este onesta, are 100% din proprietatile de securitate pe care le au nodurile intregi.
  





