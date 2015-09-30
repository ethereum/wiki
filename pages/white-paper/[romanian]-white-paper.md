---
name: White Paper
category: 
---

###Ethereum: O platforma de aplicatii descentralizata ce faciliteaza crearea de contracte inteligente de ultima generatie.

In ultimele cateva luni s-a manifestat un interes tot mai mare fata de utilizarea blockchain-urilor de tip Bitcoin- mecanismul care da intregii lumi sansa sa ajunga la un consens in privinta unei baze de date publice- pentru mai mult decat bani.Aplicatii frecvent citate includ utilizarea bunurilor digitale on-blockchain pentru a reprezenta valute personalizate si instrumente financiare ("[colored coins](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit)"), dispozitive ["smart property"](https://en.bitcoin.it/wiki/Smart_Property) cum ar fi masinile, care pot urmari un "colored coin" pe un blockchain pentru a determina proprietarul legitim al acestora, insa exista si aplicatii mult mai avansate cum ar fi schimburi decentralizate, derivate financiare,peer-to-peer gambling(?) si sisteme on-blockchain de identitate/reputatie. Dintre toate aceste tipuri de aplicatii, poate cel mai ambitios este conceptul de agenti autonomi sau [Organizatii Autonome Descentralizate](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/) (DAOs) - entitati autonome care opereaza in blockchain fara nici un fel de control central, evitand orice dependenta de contracte legale si statuturi organizatorice in favoarea resurselor si fondurilor administrate autonom de un smart-contract auto-impus pe un blockchain cryptografic.

Totusi, majoritatea acestor aplicatii sunt dificil de implementat astazi, din simplul motiv ca sistemele de scripting ale Bitcoin, si chiar ale protocoalelor cryptocurrency de ultima generatie, cum ar fi protocoalele "colored coin" si asa-numitele "metacoins", sunt mult prea limitate pentru a permite metodele arbitrare complexe de calcul pe care DAO le solicita. Ceea ce intentioneaza acest proiect sa faca este sa preia inovatia adusa de astfel de protocoale si sa le generalizeze- create a fully-fledged, Turing-complete (but heavily fee-regulated) cryptographic ledger that allows participants to encode arbitrarily complex contracts, autonomous agents and relationships that will be mediated entirely by the blockchain. In loc sa se limiteze la un tip specific de tranczactii, utilizatorii vor putea folosi Ethereum ca un fel de "Lego de crypto-finante"-altfel spus, acestia vor putea implementa orice proprietate doresc doar prin codarea in limbajul scriptic intern protocolului.Valutele personalizate, derivatele financiare, sistemele de identitate si organizatiile decentralizate vor fi usor de facut, dar, mai important,spre deosebire de sistemele anterioare, va fi posibila construirea unor tipuri de tranzactii pe care nici macar dezvoltatorii Ethereum nu le-au prevazut.Per ansamblu, credem ca acest proiect este un pas in fata spre realizarea "cryptocurrency 2.0"; speram ca Ethereum va fi la fel de important in ecosistemul cryptocurrency pe cat a fost de semnificativa aparitia Web 2.0 pentru static-content-only internet in 1999.

### Continut

* [De ce o noua platforma](#de-ce-o-noua-platforma)
    * [Colored Coins](#colored-coins)
    * [Metacoins](#metacoins)
* [Filozofie](#philosophy)
* [Constructia de blocuri de date](#basic-building-blocks)
    * [Implementarea GHOST modficata](#modified-ghost-implementation)
    * [Ethereum Client P2P Protocol](#ethereum-client-p2p-protocol)
    * [Moneda si emitere](#currency-and-issuance)
    * [Formatul datei](#data-format)
    * [Algoritmul de minare](#mining-algorithm)
    * [Tranzactii](#transactions)
    * [Adjustarea dificultatii](#difficulty-adjustment)
    * [Rasplata pentru blocuri](#block-rewards)
* [Contracte](#contracts)
    * [Aplicatii](#applications)
    * [Sub-monede](#sub-currencies)
    * [Derivate financiare](#financial-derivatives)
    * [Sisteme de reputatie si identitate](#identity-and-reputation-systems)
    * [Organizatii Autonome Descentralizate](#decentralized-autonomous-organizations)
    * [Aplicatii ce vor urma](#further-applications)
    * [Cum functioneaza contractele?](#how-do-contracts-work)
    * [Specificatii referitoare la limbajul de programare](#language-specification)
* [Taxe](#fees)
* [Concluzii](#conclusion)
* [References and Further Reading](#references-and-further-reading)


## De ce o noua platforma

Cand se doreste creearea unei noi aplicatii, in special intr-un domeniu delicat ca al criptografiei sau cryptocurrency, primul si cel mai corect instinct este acela de a se folosi protocoale existente pe cat mai mult posibil. Nu este nevoie sa se creeze un nou etalon monetar, sau chiar un nou protocol atunci cand problema poate fi rezolvata prin utilizarea tehnologiilor deja existente. Intradevar, complexitatea incercarilor de rezolvare a problemelor de smart property, smart contracts si corporatii autonome descentralizate, pe langa Bitcoin, este modul in care a aparut interesul fata de protocoalele cryptocurrency de ultima generatie. In decursul cercetarii noastre, totusi, devine evident ca in timp ce protocolul Bitcoin este mai mult decat adecvat pentru valuta, basic multisignature escrow and certain simple versions of smart contracts, there are fundamental limitations that make it non-viable for anything beyond a certain very limited scope of features.


###Colored Coins

Prima incercare de a implementa un sistem de administrare pentru smart property si valute personalizate si bunuri pe un blockchain a fost construita ca un fel de supra-protocol al Bitcoin-ului, pe care multi l-au comparat, in anumite privinte cu felul in care, in suita de protocoale a internetului, HTTP functioneaza ca un strat asupra TCP-ului. Colored coins protocol este definit dupa cum urmeaza:

Un emitent al colored coin determina ca output-ul unei anumite tranzactii H:i (H being the transaction hash and i the output index)reprezinta un anumit bun si publica o "color definition" specificand output-ul acestei tranzactii alaturi de ceea de ce reprezinta(ex. 1 satoshi de la H:i = 1 gram de aur rambursabil la Stephen's Gold Company)
Others "install" the color definition file in their colored coin clients.
Cand o culoare este emisa pentru prima data, output-ul H:i este singura tranzactie output care are acea culoare.
Daca o tranzactie foloseste input-uri cu o culoare X, atunci si output-ul va avea culoarea X. De exemplu, daca posesorul lui H:i face o tranzactie imediat, pentru a imparti acest output catre cinci adrese, atunci rezultatele acestei tranzactii vor avea, de asemenea, culoarea X. Daca o tranzactie are input-uri de culori diferite, atunci o "lege a transferului culorii" sau un "nucleu al culorii" va determina ce culoare are fiecare output (ex. o implementare mai facila ar determina culoarea output-ului 0 sa fie aceeasi cu cea a input-ului 0, output-ul 1 ar avea aceeasi culoare cu input-ul 1, etc)
Cand un client colored coin observa ca primeste un nou output al tranzactiei, foloseste in algoritm back-tracing bazat pe "nucleul culorii" pentru a determina culoarea acelui output.Pentru ca regula este determinista, toti clientii vor cadea de acord pentru a stabili ce culoare (sau culori) are fiecare output.
Cu toate acestea, protocolul are cateva imperfectiuni fundamentale:

Verficarea platilor simplificate in Bitcoin 

   
Stanga: este suficient sa se prezinte doar un numar mic de noduri intr-o schema Merkel pentru a dovedi validitatea unei ramuri.
Dreapta: orice incercare de a schimba orice parte a schemei Merkel va duce in cele din urma la o inconsecventa undeva in varful latului.
Dificultatea verificarii platilor simplificate- Constructia Schemei Merkel a Bitcoin-ului permite un protocol cunoscut ca "verificarea platilor simplificate", unde un client care nu downloadeaza intregul blockchain poate determina rapid validitatea unui output al unei tranzactii prin a cere altor noduri o dovada criptografica a valabilitatii unei ramuri a schemei. Clientul tot va trebui sa downloadeze header-ele block-ului, pentru a fi sigur, dar cantitatea de date (the amount of data bandwidth ) si timpul de verificare necesar scad cu un factor de aproape o mie.Cu colored coins insa, acest proces este mult mai dificil.Motivul este acela ca nu se poate determina culoarea unui output al unei tranzactii doar prin verficarea schemei Merkel; cu atat mai mult, trebuie folosit algoritmul de scanare inversa, prelundu-se mii de tranzactii si solicitandu-se dovada schemei Merkel de valabilitate pentru fiecare in parte, inainte ca un client sa fie pe deplin satisfacut ca o tranzactie are o anumita culoare. Dupa un an de investigatii, inclunzand si ajutorul nostru, nu s-a putut gasi nici o solutie pentru aceasta problema.
Incompatibilitatea cu script-urile- dupa cum am mentionat si mai sus, Bitcoin are un sistem de scripting flexibil moderat, de exemplu permite utilizatorilor sa semneze tranzactii de forma "Eliberez output-ul acestei tranzactii oricui este dispus sa plateasca 1 BTC". Alte exemple includ contracte de asigurare, microplati eficiente si licitatii on-blockchain. Cu toate acestea, acest sistem, in mod inerent, nu tine cont de culoare, altfel spus, clientul poate face o tranzactie de forma "Eliberez output-ul acestei tranzactii oricui este dispus sa imi plateasca o gold coin definita de originea H:i",pentru ca limbajul scriptului nu stie ca exista conceputl de "culoare".O consecinta majora a acestui lucru este ca in timp ce un schimb sigur intre doua colored coins este posibil, un schimb total descentralizat devine imposibil de obtinut,in conditiile in care nu exista o modalitate de a plasa o comanda de executare pentru a vinde sau pentru a cumpara.
Aceleasi limitari ca ale Bitcoin-ului -ideal, protocoalele on-blockchain ar trebui sa suporte derivate avansate, pariuri si multe forme de transferuri conditionale.Din pacate, colored coins mostenesc limitarile Bitcoin-ului in ceea ce priveste imposibilitatea existentei atator aranjamente.
Metacoins

Alt concept, asezat deasupra Bitcoin-ului in spiritul HTTP, care este deasupra TCP-ului, este acela de "metacoins". Conceptul unui metacoin este simplu: protocolul metacoin furnizeaza calea de codare a datelor unei tranzactii metacoin in output-ul unei tranzactii Bitcoin, iar un nod metacoin functioneaza prin procesarea tuturor tranzactiilor Bitcoin si evaluarea tranzactiilor Bitcoin care sunt tranzactii metacoin valide pentru a determina balantele contului oricand.Spre exemplu, un protocol simplu metacoin poate solicita patru output-uri unei tranzactii: MARKER, FROM, TO and VALUE. MARKER ar fi o adresa de marcaj specifica pentru a identifica o tranzactie ca o tranzactie metacoin.FROM ar fi adresa de la care sunt trimise coins;TO- adresa unde sunt trimise acestea si VALUE ar fi adresa care codeaza cantitatea trimisa. Deoarece protocolul Bitcoin nu este constientizeaza metacoins, si astfel nu va respinge tranzactii metacoin invalide, protocolul metacoin trebuie sa trateze toate tranzactiile cu primul output in MARKER ca fiind valide si sa reactioneze in consecinta. De exemplu, o punere in aplicare a partii de procesare a tranzactiilor protocolului metacoin descris mai sus s-ar putea arata astfel:

    if tx.output[0] != MARKER:
        break
    else if balance[tx.output[1]] < decode_value(tx.output[3]):
        break
    else if not tx.hasSignature(tx.output[1]):
        break
    else:
        balance[tx.output[1]] -= decode_value(tx.output[3]);
        balance[tx.output[2]] += decode_value(tx.output[3]);
Avantajul unui protocol metacoin este acela ca protocolul poate permite mai tipuri de tranzactii avansate , incluzand valute personalizate, schimburi descentralizate, etc, care sunt imposibil de implementat folosind doar protocolul care sta la baza Bitcoin. Cu toate acestea, metacoins peste Bitcoin au un mare defect: verificarea platilor simplificate, deja dificil de facut cu colored coins, devine complet imposibila cu o metacoin.Motivul este acela ca se poate folosi SPV pentru a determina daca exista o tranzactie care trimite 30 metacoins la adresa X, dar in sine nu inseamna ca adresa X are 30 metacoins.Daca emitentul tranzactiei nu are 30 metacoins de la inceput si tranzactia este invalida? In cele din urma, aflarea oricarei parti din stadiul curent necesita scanarea tuturor tranzactiilor efectuate de la lansarea metacoin-ului pentru a stabili validitatea fiecarei tranzactii in parte. Astfel, devine imposibil posesia unui client sigur, fara a se downloada intregul blockchain Bitcoin.

In ambele cazuri, concluzia este urmatoarea: efortul de a construi protocoale mai avansate deasupra Bitcoin-ului, ca HTTP peste TCP, este admirabil, si este intradevar modul corect de a aborda punerea implementarea aplicatiilor descentralizate avansate.Cu toate acestea, incercarea de a construi colored coins si metacoins peste Bitcoin seamana mai mult cu construirea HTTP peste SMTP. Scopul SMTP a fost acela de a transfera email-uri , nu sa serveasca drept motor principal pentru comunicare generica pe internet, iar pentru a-l face eficace ar fi trebuit implementate practici ineficiente si inestetice arhitectural.

Ethereum rezolva problemele de scalabilitate, fiind hostat pe propriul blockchain, si prin stocarea unui "state tree" distinct in fiecare block impreuna cu o lista a tranzactiilor.Fiecare "state tree" reprezinta stadiul curent al intregului sistem , incluzand balantele adreselor si starea contractelor.Contractele Ethereum pot stoca datele in memorie(contracts are allowed to store data in a persistent memory storage). Aceasta stocare, combinata cu limbajul de scripting Turing-complete,ne permite sa codam un intreg etalon monetar intr-un singur contract, alaturi de numeroase alte tipuri de bunuri criptografice.Prin urmare, intentia Ethereum nu este aceea de a inlocui protocoalele colored coin si metacoin descrise mai sus. Mai degraba, Ethereum intentioneaza sa serveasca drept un strat fundamental superior oferind un sistem unic si puternic de scripting pe baza caruia se pot construi contracte arbitrare avansate, valute si alte aplicatii descentralizate.Daca proiectele deja existente de colored coins si metacoins s-ar muta in Ethereum , ar beneficia de SPV-ul oferit de Ethereum, de optiunea de a fi compatibile cu derivatele financiare ale Ethereum si de abilitatea de a lucra impreuna intr-o singura retea.Cu Ethereum, cineva care are o idee pentru un nou tip de contract sau tranzactie care poate imbunatati in mod considerabil ceea ce se poate face cu cryptocurrency nu ar mai trebui sa isi lanseze propria moneda; isi poate implementa simplu ideea in codul script al Ethereul. Pe scurt, Ethereum este o fundatie pentru inovatie.


###Filozofie

Designul care sta in spatele Ethereum este creat sa urmeze principiile enumerate mai jos:

Simplitate - Protocolul Ethereum trebuie sa fie cat mai simplu posibil, chiar si cu riscul unor ineficiente in ceea ce ce priveste timpul sau stocarea de date. Ideal, un programator obisnuit ar trebui sa poata urmari si implementa toate specificatiile, astfel incat sa se poata realiza pe deplin potentialul de democratizare fara precedent pe care cryptocurrency il aduce si sa promoveze Ethereum ca un protocol deschis tuturor.Orice optimizare ce adauga complexitate nu ar trebui inclusa decat in cazul in care acea optimizare poate aduce un beneficiu substential.
Universalitate - o parte fundamentala a filosofiei design-ului Ethereum este aceaa ca nu are "trasaturi". In loc de acest lucru, Ethereum asigura un limbaj de scripting intern Turing-complete, pe care un programator il poate folosi pentru a realiza orice smart contract sau tip de tranzactie ce poate fi definita matematic.Vrei sa iti realizezi propriul derivat financiar? Cu Ethereum poti face asta.Vrei sa iti faci propria valuta?Seteaz-o ca un contract Ethereum. Vrei sa instalezi Daemon sau Skynet la o scara larga?Cel mai probabil vei avea nevoie de cateva mii de contracte centralizate, si ai grija sa le feed them generously,pentru a realiza acest lucru, dar nu te opreste nimic daca ai Ethereum la dispozitie.
Modularitate - Partile protocolului Ethereum ar trebui construite modular, si, pe cat posibil, astfel incat sa poata fi separate.Pe parcursul dezvoltarii noaste, scopul este acela de a crea un program unde daca cineva ar face o mica modificare a protocolului, aplicatia ar continua sa functioneze fara alte modificari.Inovatii precum Dagger, Patricia trees si RLP trebuie implementate ca librarii separate si trebuie facute cu "trasaturi complete" chiar daca Ethereum nu are nevoie de anumite "trasaturi" pentru a le face utile si in alte protocoale.Dezvoltarea Ethereum trebuie realizata la cote maxime, spre beneficiul intrecugul ecosistem cryptocurrency, nu doar pentru al lui in sine.
Agilitate - detaliile protocolului Ethereum nu sunt de neclintit.details of the Ethereum protocol are not set in stone. Desi vom fi destul de judiciosi in legatura cu efectuarea modificarilor in constructii de nivel inalt cum ar fi limbajul de tipul C si sistemul de adrese, teste computationale efectuate ulterior ne pot conduce la concluzia ca anumite modificari ale algoritmului sau al limbajului de programare pot imbunatati considerabil scalabilitatea sau securitatea. In cazul in care se ivesc astfel de oportunitati, le vom explora.
Nondiscriminare- protocolul nu trebuie sa incerce sa resctrictioneze activ sau sa previna categorii de uzabilitate specifice.Toate mecanismele de reglementare din protocol trebuie sa fie create in asa fel incat sa regleze prejudicii si sa incerce sa nu se opuna unor anumite aplicatii nedorite specifice. Un programator poate chiar sa administreze un loop script peste Ethereum atat timp cat intentioneaza sa plateasca taxa pentru fiecare pas al tranzactiei.

Basic Building Blocks

La origine, Ethereum este a fairly regular memory-hard proof-of-work mined cryptocurrency without many extra complications. De fapt, Ethereum este , din anumite puncte de vedere, mai simplu decat cryptocurrencies bazate pe Bitcoin folosite astazi.Conceptul conform caruia o tranzactie poate avea mai multe output-uri si input-uri, de exemplu, a fost inlocuit cu model bazat pe balante mai intuitiv( pentru a preveni replay-attacks, (to prevent transaction replay attacks, as part of each account balance we also store an incrementing nonce). Sequence numbers and lock times are also removed, and all transaction and block data is encoded in a single format. Instead of addresses being the RIPEMD160 hash of the SHA256 hash of the public key prefixed with 04, addresses are simply the last 20 bytes of the SHA3 hash of the public key. Unlike other cryptocurrencies, which aim to offer a large number of "features", Ethereum intends to take features away, and instead provide its users with near-infinite power through an all-encompassing mechanism known as "contracts".


La origine, Ethereum este a fairly regular memory-hard proof-of-work mined cryptocurrency without many extra complications. De fapt, Ethereum este , din anumite puncte de vedere, mai simplu decat cryptocurrencies bazate pe Bitcoin folosite astazi.Conceptul conform caruia o tranzactie poate avea mai multe output-uri si input-uri, de exemplu, a fost inlocuit cu model bazat pe balante mai intuitiv( pentru a preveni replay-attacks, (to prevent transaction replay attacks, as part of each account balance we also store an incrementing nonce). Sequence numbers and lock times are also removed, and all transaction and block data is encoded in a single format. Instead of addresses being the RIPEMD160 hash of the SHA256 hash of the public key prefixed with 04, addresses are simply the last 20 bytes of the SHA3 hash of the public key. Unlike other cryptocurrencies, which aim to offer a large number of "features", Ethereum intends to take features away, and instead provide its users with near-infinite power through an all-encompassing mechanism known as "contracts".


Modified GHOST Implementation

Protocolul "Greedy Heavist Observed Subtree" (GHOST) este o inovatie  introdusa de Yonatan Sompolinsky si  Aviv Zohar in Decembrie 2013. Motivul din spatele GHOST este acela ca in prezent, blockchains cu rata mare de confirmare sufera de probleme de securitate datorate unui stale rate ridicat – deoarece block-urile necesita un anume timp pentru a circula in retea; daca un miner A exploateaza un block si minerul B exploateaza un alt block, inainte ca block-ul minerului A sa se propage catre minerul B, block-ul minerului B se va corupe, si nu va contribui la securitatea retelei.Mai mult de atat, exista problema centralizarii: daca minerul A reprezinta un mining pool cu un hashpower de 30% si minerul B are hashpower de 10%, A risca sa produca stale blocks 70% din timp, iar B sa produca block-uri invalide 90% din timp. Astfel, daca stale rate este ridicat, A va fi mult mai eficient, numai multumita marimii sale.Combinand aceste doua efecte, acele blockchains care produc block-uri mai rapid sunt mai probabil sa conduca la un mining pool -cu un procentaj mare din hashpower-ul retelei- care sa aiba control efectiv asupra procesului de mining.

Asa cum descriu Sompolinsky si Zohar, GHOST rezolva prima problema a pierderilor de securitate ale retelei prin includerea stale blocks in calculul care determina care lant este mai lung: asta inseamna ca nu numai predecesorii unui block, dar si stale blocks care deriva din acesti predecesori („unchi” in jargonul Ethereus) sunt luati in calcul in a determina care block are cea mai vizibila dovada de munca ca sa il sustina. Pentru a rezolva aceasta problema secundara a centralization bias, trebuie sa mergem dincolo de protocolul descris de Sompolinsky si  Zohar, si sa atribuim block rewards pentru stale blocks: un stale block primeste 87.5% din base reward, si „nepotul” care include stale block-ul primeste cei 12.5% ramasi. Taxele de tranzactie totusi, nu sunt acordate „unchilor”.

Ethereum implementeaza o versiune simplificata a GHOST care merge in jos doar un nivel. Un stale block poate fi inclus ca „unchi” numai de catre descendentul direct al unuia din „fratii” lui, nu si al altor block-uri mai indepartate. Aceasta s-a facut pentru mai multe motive. In primul rand, un protocol GHOST nelimitat ar include prea multe complicatii in calculul numarului de „unchi” valizi pentru fiecare block. In al doilea rand, un GHOST nelimitat, cu o compensare ca aceea folosita de Ethereum nu mai constrange un miner sa actioneze pe chain-ul principal si nu pe chain-ul unui atacator public. In ultimul rand,  calculele arata ca GHOST la single-level are 80% din avantajele unui GHOST nelimitat, si ofera un stale rate comparabil cu Litecoin de 2.5 minute chiar si cu un block time de 40 de secunde. Totusi, ca sa nu exageram, vom pastra un block time de 60 de secunde, specific pentru Primecoin, deoarece block-urile individuale pot necesita mai mult timp pentru a fi verificate.


Ethereum Client P2P Protocol

P2P Protocol 


Clientul P2P de protocol Ethereum este un protocol standard de cryptocurrency, si poate fi folosit la fel de usor pentru alte tipuri de cryptocurrency; singura modificare este introducerea protocolului GHOST, descris mai sus. Clientul Ethereum va fi in mare parte reactiv; daca nu e provocat, singurul lucru care il va face de unul singur este sa determine daemon-ul de retea, sa mentina conexiunile, si sa trimita periodic un mesaj prin care sa ceara block-uri derivate din block-ul curent. De asemenea, clientul va fi mai puternic. Spre deosebire de Bitcoin, care stocheaza o cantitate limitata de date despre blockchain, clientul Ethereum va opera si ca un back-end functional pentru un block explorer.

Cand clientul citeste un mesaj, va parcurge pasii urmatori:

Hash the data, and check if the data with that hash has already been received. If so, exit.
Stabileste tipul de date. Daca datele sunt tranzactii, daca tranzactia este valida, este adaugata la lista de tranzactii locale, e procesata pe block-ul curent, si emisa pe retea. Daca datele reprezinta un mesaj, se raspunde la acesta. Daca datele reprezinta un block, se trece la pasul 3.
Verificati daca „parintele” unui block este deja stocat in baza de date. Daca nu este, iesiti.
Verificati daca este validat lucrul pe header-ul block-urilor din lista de „unchi”. Daca nu, iesiti.
Verificati daca fiecare block header  din lista de „unchi” din block are ca parinte la randul lui fiecare parinte al block-ului respectiv. Daca nu, iesiti. Tineti minte ca block headers care sunt „unchi” nu trebuie sa fie in baza de date; ei trebuie doar sa aiba parintele corect si dovada de lucru. De asemenea, asigurati-va ca „unchii” sunt unici, si separati de parinte.
Verificati daca marcajul de timp al block-ului este cu maxim 15 minute in viitor, si inaintea timpului parintelui. Verificati daca dificultatea block-ului si numarul acestuia sunt corecte. Daca vreuna din aceste verificari esueaza, iesiti.
Incepeti cu starea parintelui blocului, si aplicati succesiv pe acesta fiecare tranzactie din block. La sfarsit, adaugati reward-urile minerului. Daca root hash a state tree-ului rezultat nu se potriveste cu state root din block header, iesiti. Daca se potriveste, adaugati blocul la baza de date si avansati la pasul urmator
Determinati TD(block) ("total difficulty") pentru noul block. TD se defineste prin TD(genesis_block) = 0 si TD(B) = TD(B.parent) + sum([u.difficulty for u in B.uncles]) + B.difficulty.Daca noul block are un TD mai mare decat block-ul curent, setati noul block pentru block-ul curent, si continuati la pasul urmator. Daca nu, iesiti.
Daca noul block a fost schimbat, aplicati toate tranzactiile in lista de tranzactii, omiteti-le pe cele invalide, si retrimiteti block-ul si tranzactiile pe retea.
„Current block” este un indicator pastrat de fiecare nod care se refera  la blocul considerat reprezentativ pentru starea actuala a retelei. Toate mesajele care cer informatii referitoare la sold, starea contractelor etc, au raspunsurile calculate in raport cu current block. Daca un nod este in procesul de mining, procedeul este doar putin alterat: cand se fac operatiile  de mai sus, nodul mineaza continuu current block-ul, folosindu-i lista de tranzactii ca si lista de tranzactii pentru block.


Currency and Issuance(Moneda si emiterea ei)

Reteaua Ethereum are o moneda inclusa, numita „ether”.  Exista doua motive pentru includerea unei monede in retea. In primul rand, ca si Bitcoin, ether este acordat minerilor pentru a stimula securitatea retelei.In al doilea rand, serveste ca metoda de plata pentru servicii anti-spam. Din cele doua alternative la taxa, dovada de lucru pentru fiecare tranzactie similara cu Hashcashsi metoda laissez-faire fara taxa, prima nu este eficienta cu resursele si prea punitiva pentru calculatoare slabe si smartphone-uri, pe cand cea din urma ar cauza ca reteaua sa fie suprasolicitata imediat de contracte tip „logic bomb” repetate. Pentru convenienta, si pentru a evita viitoare dispute (vezi dezbateream BTC/uBTC/satoshi), denominatiile vor fi pre-marcate:

Dupa 1 an	 Dupa 5 ani
Currency units	 1.9X	 3.5X
Fundraiser participants	 52.6%	 28.6%
Fiduciary members and early contributors	 11.8%	 6.42%
Alocatii pre-lansare aditionale	 2.63%	 1.42%
Rezerve	 11.8%	 6.42%
Miners	 21.1%	 57.1%
1: wei
103: (nespecificat)
106: (nespecificat)
109: (nespecificat)
1012: szabo
1015: finney
1018: ether
Acestea trebuie vazute ca o viziune expandata a conceptelor de ""dolari" si "centi" sau ca "BTC" si "satoshi" reprezinta viitorul.Szabo, finney si ether putin probabil vor fi folosite in viitorul apropiat, in timp ce celelalte unitati vor fi folosite in continuare la fel de mult."ether" este destinata sa fie unitatea primara in sistem, cum este spre exemplu dolarul sau bitcoin-ul.Dreptul de a numi unitati 103, 106 si 109 va fi lasat ca recompensa secundara de nivel inalt pentru obiectul strangerii de fonduri, cu pre-aprobare de la noi.

Modelul de emitere va arata in modul urmator:

Ether va fi lansat intr-o strangere de fonduri la pretul de 1000-2000 ether per BTC, primii investitori primind preturi mai bune pentru a compensa numarul incert de mare al participantilor intr-o faza incipienta.Suma minima de finantare va fi de 0.01 BTC. Presupunem ca X ether sunt lansati in acest mod.
0.225X ether vor fi alocati membrilor fiduciari si primilor contribuabili care s-au implicat substantial in proiect, inaintea inceperii strangerii de fonduri. Aceasta parte va fi stocata intr-un contract time-lock, aproximativ 40% va putea fi folosita dupa un an, 70% dupa doi ani, iar 100% dupa 3 ani..
0.05X ether vor fi alocati pentru un fond folosit pentru platile cheltuielilor si premiilor in ether in perioada dintre stratul strangerii de fonduri si lansarea valutei.
0.225X ether vor fi alocati ca rezerva pe termen lung pentru a plati cheltuieli, salarii si premii dupa lansarea valutei.
0.4X ether will be mined per year forever after that point
Rata de inflatie pe termen lung(procentaje) 

 
Despite the linear currency issuance, just like with Bitcoin over time the inflation rate nevertheless tends to zero
De exemplu, dupa cinci ani si presupunand ca nu s-au realizat tranzactii, 28.6% din ether va reveni primilor participanti la strangerea de fonduri, 6.42% membrilor fiduciari si primilor contribuabili, 6.42% platiti pentru rezerve si 57.1% -miners Modelul permanent linear de inflatie reduce riscul unei concentrari de resurse financiare destul de mare in Bitcoin, dupa cum ar vedea unii, si da sanse egale indivizilor care traiesc in prezent, dar si celor din viitor, sa obtina unitati valutare,in acelasi timp retinand stimulente puternice pentru a obtine si pentru a pastra ether, pentru ca "rata" de inflatie tot va tinde catre zero pe parcursul timpului(ex. pe parcursul anului 1000001 "proviziile" de bani vor creste de la 500001.5 * X la 500002 * X, cu o rata de inflatie de 0.0001%).Mai mult decat atat, o mare parte din interesul in Ethereum va fi pe termen mediu, prezicem ca daca Ethereum va fi un succes,vom vedea cea mai mare parte a cresterii sale pe un interval de timp 1-10 ani, iar aprovizionarea in aceasta perioada va fi foarte limitata.

De asemenea, sustinem ca, deoarece monedele sunt intotdeauna pierdute in timp din cauza neglijentei, etc, si pierderea monedei poate fi modelata ca un procent din totalul ofertei pe an, ca totalul valutei aflat in circulatie se va stabiliza, in cele din urma, la o valoare echivalenta cu emiterea anuala impartita la rata de pierdere (de exemplu, la o rata de pierdere de 1%, odată ce alimentarea ajunge 40X atunci 0.4X vor fi exploatate si 0.4X pierdute in fiecare an, creand un echilibru).


Data Format

Toate datele din Ethereum vor fi stocate in recursive length prefix encoding, care serializeaza matrice de siruri de caractere de lungime si dimensiuni arbitrare in siruri de caractere. De exemplu, ['caine', 'pisica'] este serializat (in byte array format) ca [ 130, 67, 100, 111, 103, 67, 99, 97, 116]; ideea generala este sa se codeze tipul de date si lungimea acestora intr-un singur byte urmat de datele efective (ex. transformata intr-o matrice byte, 'caine' devine [ 100, 111, 103 ], deci serializarea sa este[ 67, 100, 111, 103 ].Dupa cum este sugerata si de numele sau, codarea RLP este recursiva; atunci cand folosim codarea RLP pentru o matrice, de fapt codam un sir care este concatenarea codificarii RLP al fiecarui element. In plus, retineti ca numarul de bloc, marcajele de timp, dificultatea, depozitele de memorie, soldurile conturilor si toate valorile din contractul de depozitare sunt numere intregi, si Patricia tree hashes,root hashes, adresele, listele tranzactii hash si toate cheile in depozite de contract sunt siruri de caractere.Principala diferenta dintre cele doua este ca sirurile de caractere sunt stocate ca date de lungime fixa (20 bytes pentru adrese, 32 bytes pentru orice altceva), si numerele intregi ocupa atat spatiu cat au nevoie. Numerele intregi sunt stocate intr-o baza de format 256 big-endian(ex. 32767 in format de matrice byte ca [ 127, 255 ]).

Un block intreg este stocat ca:

    [
        block_header,
        transaction_list,
        uncle_list 
    ]
Unde:

    transaction_list = [
        transaction 1,
        transaction 2,
        ...
    ]

    uncle list = [
        uncle_block_header_1,
        uncle_block_header_2,
        ...
    ]

    block_header = [
        parent hash,
        sha3(rlp_encode(uncle_list)),
        coinbase address,
        state_root,
        sha3(rlp_encode(transaction_list)),
        difficulty,
        timestamp,
        extra_data,
        nonce
    ]
Fiecare tranzactie si header de block unchi este in sine o lista.Datele pentru dovada de functionare este codarea RLP a block-ului FARA nonce. uncle_list si transaction_list sunt listele de headere block unchi si tranzactiile din block, respectiv . nonce si extra_data sunt ambele limitate la un maxin de 32 bytes, exceptand genesis block unde parametriiextra_data vor fi mult mai mari.

State_root este radacina unei scheme Merkle Patricia continand (cheie, valoare) perechi pentru toate conturile in care fiecare adresa este reprezentata de un sir de caractere binar de 20 bytes. La adresa fiecarui cont in parte , valoarea stocata in schema Merkel Patricia este un sir de caractere serializarea RLP a unui obiect de forma:

    [ balance, nonce, contract_root, storage_deposit ]
nonce este numarul de tranzactii facute din cont si este incrementat de fiecare data cand este realizata o tranzactie.Scopul este acela de (1) a face o tranzactie valida o singura data pentru a preveni atacurile, si (2) pentru a face imposibil (mai exact cryptografic nefezabil) construirea unui contract cu acelasi hash ca al unui contract existent. balance se refera la balanta contului, denominata in wei. contract_root este radacina unei alte scheme Patricia, continand memoria contractului, daca acel cont este controlat de un contract.Daca un cont nu este controlat de un contract, contract root va fi un sir de caractere gol. storage_deposit este un contor care stocheaza taxele de depozitare achitate; functia sa va fi discutata in detaliu mai jos.

 


Mining algorithm

O proprietate de dorit in mining algorithms este rezistenta la optimizare prin hardware specializat. La inceput, Bitcoin a fost conceput ca o moneda democratica, dand oricui sansa de a participa in procesul de mining cu un CPU. In 2010 totusi, mineri mai rapizi care exploatau paralelizarea oferita de procesoare video (GPU) au preluat controlul, crescand network hashpower cu un factor de 100, si lasand CPU-urile in urma. In 2013, o alta categorie de hardware specializat, application-specific integrated circuits (ASICs) a depasit ca performante GPU-urile, atingand viteze de 100 de ori mai mari, folosind chip-uri fabricate special pentru computarea SHA256 hashes. Astazi, este practic imposibila minarea fara achizitionarea unui mining device de la una din aceste companii, si exista ingrijorari ca in 5-10 ani, mining-ul va fi dominat in intregime de de corporatii centralizate precum AMD sau Intel.

Pana acum, calea principala de a atinge acest scop a fost "memory-hardness", construirea unor algoritmi proof of work care necesita nu numai un numar mare de calcule, dar si multa memorie pentru validare, astfel reducand eficacitatea implementarilor de hardware paralelizat si specializat. Exista cateva implementari de memory-hard proof of work, fiecare cu defectele ei:

Scrypt - este o functie proiectata sa ocupe 128 kb de memorie pentru calcul. In esenta, algoritmul  functioneaza prin ocuparea unui memory array cu hashes, si apoi calculand valori intermediare si finalmente, un rezultat bazat pe aceste valori din memory array.Totusi, parametrul de 128 kb este o limitare, si ASICs pentru Litecoin sunt deja in proces de dezvoltare. In plus, exista o limita naturala pe cantitatea de memory hardness in Scrypt care poate fi atinsa, caci procesul de verificare necesita la fel de mult calcul si memorie, ca o etapa a procesului de mining.
Birthday attacks - ideea din spatele birthday-based proofs of work este simpla: gasiti valorile xn,i,j astfel incat i < k, j < k si|H(data+xn+i) - H(data+xn+j)| < 2^256 / d^2. Parametrul d seteaza dificultatea calculului de a gasi un block, si parametrul k seteaza memory hardness. Orice birthday algorithm trebuie sa stocheze cumva toate calculele de H(data+xn+i)in memorie, astfel ca viitoarele calcule sa fie comparate cu acestea. Aici, calculul este memory-hard, dar verificarea este memory-easy, permitand extreme memory hardness fara a compromite usurinta verificarii.Totusi, algoritmul este problematic din doua motive. In primul rand, exista un time-memory tradeoff attack, unde utilizatorii cu 2x mai putina memorie pot compensa cu 2x putere de calcul, prin urmare memory hardness nu este absoluta. In al doilea rand, echipamentul hardware specializat poate fi usor de construit, in special daca se trece de traditionala arhitectura pe baza de chip si procesor, catre diverse clase de hash tables bazate pe hardware sau probabilistic analog computing.
Dagger - ideea din spatele Dagger,, un algoritm in-house dezvoltat de echipa Ethereum, este aceea de a avea un algoritm similar lui Scrypt, dar proiectat special ca fiecare nonce sa depinda numai de o mica portiune din data tree care rezulta din fiecare grup de ~10 milioane nonces. Calculul nonces la un nivel rezonabil de eficienta solicita construirea unui intreg tree, ocupand 100mb de memorie, pe cand verificarea unui nonce necesita doar 100kb. Totusi, aloritmii in genul Dagger sunt vulnerabili la dispozitive cu circuite computationale multiple care impart aceeasi memorie, si desi acest pericol poate fi diminuat, este probabil imposibil de inlaturat complet.
Ca un standard, avem in vedere un algoritm in genul Dagger cu parametri ajustati sa minimizeze atacurile de hardware specializat, probabil impreuna cu un proof of stake algorithm ca propriul nostru Slasher, pentru securitate sporita, daca este necesar. Totusi, pentru a veni cu un proof-of-work algorithm mai bun ca al competitiei, intentia noastra este sa folosim o parte din fondurile stranse pentru a organiza o competitie cu premii, similara cu cele pentru a determina algoritmul pentru Advanced Encryption Standard (AES) in 2005 si SHA3 hash algorithm in 2013, unde grupuri de cercetare din toata lumea concureaza pentru a dezvolta mining algorithms rezistenti la ASIC, si sa avem un proces de selectie cu mai multe runde de jurizare pentru a alege invingatorii. Incurajam cercetari despre memory-hard proofs of work, self-modifying proofs of work, proofs of work bazate pe instructiuni x86, multiple proofs of work cu protocoale economice ca stimulente pentru dezvoltare, si orice design care atinge scopul dorit. De asemenea vor exista oportunitati de a explora alternative precum proof of stake, proof of burn si proof of excellence.

 


Tranzactii

O tranzactie este stocata astfel:

[ nonce, receiving_address, value, [ data item 0, data item 1 ... data item n ], v, r, s ]
nonce este numarul de trnazactii deja trimise de acel cont, codate in forma binara(ex. 0 -> '', 7 -> '\x07', 1000 -> '\x03\xd8'). (v,r,s) este semnatura bruta Electrum-style a tranzactiei fara ca acea semnatura sa fie facuta cu private key corespunzatoare contului expeditor, cu 0 <= v <= 3. De la o semnatura Electrum-style (65 bytes) este posibila extractia public key, si , deci, si adresa, in mod direct.O tranzactie valida este una in care (i) semnatura este bine formata(ie. 0 <= v <= 3, 0 <= r < P, 0 <= s < N, 0 <= r < P - N if v >= 2), si (ii) contul expeditor are destule fonduri pentru a plati taxele si valoarea.Un block valid nu poate contine o tranzactie invalida; cu toate acestea, daca un contract genereaza o tranzactie invalida, acea tranzactie pur si simplu nu va avea efect.Daca cineva doreste sa plateasca voluntar o taxa mai mare, este liber sa faca asta, prin construirea unui contract care trimite mai departe tranzactia , dar care trimite automat o suma sau un procentaj minerului block-ului curent.

Tranzactiile trimise la un sir gol , ca adresa, sunt tranzactii speciale, creand un "contract".


Difficulty adjustment( Adjustarea dificultatilor)

Dificultatea este adjustata prin formula:

D(genesis_block) = 2^36
D(block) =
    if anc(block,1).timestamp >= anc(block,501).timestamp + 60 * 500: D(block.parent) - floor(D(block.parent) / 1000)
    else:                                                             D(block.parent) + floor(D(block.parent) / 1000)
anc(block,n)este generatia "n" ascendenta a block-ului; toate block-urile dinaintea block-ului geneza(original) se presupune ca au acelasi timestamp ca block-ul geneza.Acesta se stabilizeaza automat in jurul a 60 de secunde. Alegerea valorii de 500 a fost facuta pentru a balansa preocuparea ca minerii cu valori mai mici , cu hashpower suficient pentru a produce des doua block-uri la rand ,ar avea imboldul de a da un timestapt incorect pentru a-si maximiza propria rasplata si, pentru ca, odata cu valorile mari si dificultatea oscileaza prea mult, cu constanta de 500 simularile arata ca un hashpower constant produce o variatie de aproximativ +/-20%.


Block Rewards

Un miner primeste trei tipuri de rasplata: o rasplata static block pentru producerea unui block, platile din tranzactii si recompense "nepot/unchi" descrise in sectiunea GHOST de mai sus.Minerul va primi 100% din recompensa block, dar plata pentru tranzactie va fi impartita, astfel 50% din ea merge la miner, iar restul de 50% este impartit in mod egal intre ultimii 64 de mineri.Motivul pentru aceasta impartire este unul de preventie impotriva creerii de catre un miner a unui block Ethereum cu un numar nelimitat de operatii, care sa plateasca taxele de tranzactii catre ei insisi, mentinand in acelasi timp un stimulent pentru ca alti mineri sa includa tranzactii.Dupa cum am descris in in sectiunea GHOST, unchii primesc doar 87.5% din rasplata block, celelalte 12.5% procente ramanand la nepotul fiecaruia in parte, platile pentru tanzactiile provenite din block-uri vechi nu merg la nimeni.


Contracts

In Ethereum exista doua tipuri de entitati care pot genera si pot primi tranzactii: persoane reale(sau bots, protocoalele cryptografice nu ii pot distinge) si contracte. Un contract este, esential , un agent automat care traieste in reteaua Ethereum, are o adresa si o balanta Ethereum, si care poate primi si trimite tranzactii.Un contract este "activat" de fiecare data cand cineva trimite o tranzactie spre el, punct in care isi ruleaza codul, poate modificandu-si starea interna sau chiar trimitand anumite tranzactii, dupa care se inchide (shut down)."Codul" pentru un contract este scris special intr-un limbaj de un nivel scazut, constand intr-o stiva , care nu este persistenta, si2256 storage entries care constituie starea permanenta a contractului. Retineti ca utilizatorii Ethereum nu vor trebui sa codeze in acest limbaj, vom furniza un C-like language cu variabile, expresii, conditionale, tablouri si while loops, si ofera un compiler down to Ethereum script code.


Applications

Aici sunt cateva exemple a ceea ce se poate face cu contractele Ethereum, cu toate exemplele de coduri scrise in our C-like language. Variabileletx.sender, tx.value, tx.fee, tx.data si tx.datan sunt proprietati ale tranzactiilor viitoare, contract.storage, si contract.address ale contractului in sine, si block.contract_storage, block.account_balance, block.number, block.difficulty, block.parenthash, block.basefee si block.timestamp proprietati ale block-ului. block.basefee este "taxa de baza" in care toate taxele de tranzactii din Ethereum sunt calculate ca multipli (is the "base fee" which all transaction fees in Ethereum are calculated as a multiple of; for more info see the "fees" section below). Toate variabilele exprimate prin majuscule (ex. A) sunt constante, pentru a fi inlocuite de valori adevarate de creatorul contractului, in momentul in care acel contract este eliberat.


Sub-currencies

Sub-currencies (sub-valutele) au multe aplicatii, de la valute care reprezinta bunuri ca dolari sau aur la actiuni si chiar valute emise cu o singura unitate pentru a reprezenta obiecte de colectie sau proprietate intelectuala (smart property).Protocoale financiare avansate cu destinatie avansata care se afla pe Ethereum pot dori sa se organizeze cu o valuta interna.Sub-currencies sunt surprinzator de usor de implementat in Ethereum, aceasta sectiune descrie un contract destul de simplu pentru acest lucru.

Ideea este aceea ca daca cineva doreste sa trimita x unitati de valuta contului A in valuta contractului C,vor trebui sa faca o tranzactie de format(C, 100 * block.basefee, [A, X]), iar contractul analizeaza tranzactia si adjusteaza balanta corespunzator.Pentru ca o tranzactie sa fie valabila, trebuie trimisa de 100 de ori taxa de baza in ether, pentru a alimenta contractul (and the contract parses the transaction and adjusts balances accordingly. For a transaction to be valid, it must send 100 times the base fee worth of ether to the contract in order to "feed" the contract (cu fiecare pas computational ce urmeaza dupa primii 16 contractul va fi taxat cu o mica plata si contractul va inceta sa functioneze daca balanta sa ajunge la zero).

    if tx.value < 100 * block.basefee:
        stop
    elif contract.storage[1000]:
        from = tx.sender
        to = tx.data[0]
        value = tx.data[1]
        if to <= 1000:
            stop
        if contract.storage[from] < value:
            stop
        contract.storage[from] = contract.storage[from] - value
        contract.storage[to] = contract.storage[to] + value
    else:
        contract.storage[mycreator] = 10^18
        contract.storage[1000] = 1
Programatorii sub-valutelor Ethereum pot , deasemenea, sa doreasca adaugarea unor alte trasaturi mai avansate:

Includerea unui mecanism prin care oamenii pot cumpara unitati valutare in schimbul ether-ilor, poate licitand un numar fix de unitati zilnic.
Permiterea platii taxelor de tranzactii in valute interne, si apoi rambursarea taxei pentru tranzactiile ether expeditorului.Acest lucru rezolva una din problemele majore pe care toate celelalte protocoale "sub-valuta" le-au avut pana in momentul de fata: faptul ca utilizatorii de sub-valute trebuie sa mentina o balanta de unitati de sub-valute pentru utilizare si unitati in principala valuta pentru a plati taxele pentru tanzactii.Aici, un cont nou ar trebui sa fie "activat" o data cu ether-ul, dar de aici mai departe nu ar mai trecui reincarcat.
Permiterea unui schimb trust-free descentralizat intre valuta si ether. Retineti ca acest schimb trust-free descentralizat intre orice doua contracte este teoretic posibil in Ethereum chiar si fara un suport special, dar suportul special va permite ca procesul sa fie realizat de aproximativ zece ori mai ieftin.

Derivate financiare

Ingredientul-cheie care sta la baza derivatelor financiare este un data feed care furnizeaza pretul unui bun, asa cum e desemnat in alt bun (in cazul Ethereum, al doilea bun va fi de obicei ether). Sunt mai multe cai de a implementa un data feed; o metoda, implementata de creatorii Mastercoin, consta in includerea data feed in blockchain. Codul este acesta:

    if tx.sender != FEEDOWNER:
        stop
    contract.storage[data[0]] = data[1]
Orice alt contract va putea apoi sa interogheze index-ul I din data store D folosind block.contract_storage(D)[I]. Un mod mai avansat de a implementa un data feed este efectuarea acestei operatii off-chain – provider-ul de data feed sa semneze toate valorile, facand posibila incercarea de a declansa contractul numai prin includerea signed data, si apoi folosirea functiei de internal scripting a Ethereum pentru a verifica semnatura. In mare, orice derivat poate fi efectuat astfel, incluzand leverage trading, optiuni si operatiuni mai avansate precum collateralized debt obligations (no bailouts here though, so be mindful of black swan risks).

Pentru a exemplifica, facem un contract de hedging. Ideea de baza este ca acel contract este creat de o persoana A, care depune 4000 ether. Contractul este apoi disponibil pentru acceptare de catre altcineva, care mai depune 1000 ether. Sa spunem ca 1000 ether valoreaza 25$ cand este facut contractul, potrivit indexului I din data store D. Daca persoana B accepta, dupa 30 de zile oricine poate trimite o tranzactie pentru a face contractul sa proceseze, trimitand acelasi echivalent de dolari in ether (in exemplul nostru, 25$) inapoi la B si restul catre A. B are avatajul de a fi protejat de currency volatility risk, fara a fi nevoit sa depinda de alti emitenti. Singurul risc pe care si-l asuma B este daca valoarea ether scade cu 80% in 30 de zile – chiar si atunci, daca B este online, se poate transfera catre un alt hedging contract. Avantajul lui Aeste taxa implicita de 0.2% din contract, si A se poate feri de pierderi daca detine valuta (USD) in alte locatii (sau Apoate fi optimist in legatura cu viitorul Ethereum, si vrea sa mentina ether la un leverage de 1.25x, caz in care taxa ar fi in favoarea lui B).

    if tx.value < 200 * block.basefee:
        stop
    if contract.storage[1000] == 0:
        if tx.value < 1000 * 10^18:
            stop
        contract.storage[1000] = 0
        contract.storage[1001] = 998 * block.contract_storage(D)[I]
        contract.storage[1002] = block.timestamp + 30 * 86400
        contract.storage[1003] = tx.sender
    else:
        ethervalue = contract.storage[1001] / block.contract_storage(D)[I]
        if ethervalue >= 5000 * 10^18:
            mktx(contract.storage[1003],5000 * 10^18,0,0)
        else if block.timestamp > contract.storage[1002]:
            mktx(contract.storage[1003],ethervalue,0,0)
            mktx(A,5000 - ethervalue,0,0)
Si alte contracte financiare avansate sunt posibile; optiuni de clauze complexe (ex: “Oricine, denumit in continuare X, poate revendica acest cont prin depunerea a 2$ pana la 1 decembrie). X va avea de ales pe 4 decembrie intre a primi 1.95$ pe 29 decembrie si dreptul de a se decide pe 11 decembrie intre 2.20$ pe 29 decembrie si dreptul de a alege pe 18 decembrie intre suma de 1.89 GBP si a plati 1 EUR si a primi 3.20 EUR pe 29 decembrie) pot fi definite mai simplu prin stocarea unui state variable la fel ca in contractul mentionat, dar cu mai multe clauze in cod, cate una pentru fiecare stare posibila. Tineti minte ca orice forma de contract financiar trebuie sa fie colaterale; reteaua Ethereum nu detine o agentie de executare silita si nu poate colecta datorii.


Identity and Reputation Systems

Cea mai veche alternativa cryptocurrency, Namecoin,a incercat sa foloseasca un blockchain specific Bitcoin pentru a furniza un sistem de inregistrare a numelor, unde utilizatorii isi pot inregistra numele si si alte date intr-o baza de date publica. Cel mai folosit caz este un sistem DNScare alocheaza nume de domenii precum “bitcoin.org” (sau in cazul Namecoin, “bitcoin.bit”) unei adrese IP. In alte cazuri se includ autentificarea prin e-mail si posibil alte advanced reputation systems. Aici este un contract simplu pentru un sistem de inregistrare a numelor in genul Namecoin, in Ethereum:

    if tx.value < block.basefee * 200:
        stop
    if contract.storage[tx.data[0]] or tx.data[0] < 100:
        stop
    contract.storage[tx.data[0]] = tx.data[1]
Se poate adauga mai multa complexitate, pentru a permite utilizatorilor sa schimbe mappings, sa trimita tranzactii automat catre contract, de unde sa fie retrimise, si chiar sa adauge reputatie si web-of-trust mechanics.


Organizatii Descentralizate Autonome

Conceptul general din spatele Organizatiilor Descentralizate Autonome este acela ca o entitate virtuala are un anumit numar de membri sau actionari, care daca detin 67% din actiuni, sunt autorizati sa foloseasca fondurile entitatii, si sa modifice codul. Membrii ar decide impreuna cum ar trebui alocate fondurile organizatiei. Metodele de alocare pot fi diverse: de la cadouri si salarii pana la implementarea unei monede interne sau bonusuri. In esenta, aceasta imita metodele folosite de o companie traditionala sau organizatie non-profit, doar ca foloseste tehnologia cryptographic blockchain. Pana acum discutiile legate de DAO au vizat modelul “capitalist” al corporatiilor descentralizate autonome (DAC) cu actionari care primesc dividende; o alternativa ar permite tuturor actionarilor sa aiba un rol egal in luarea deciziilor, si un numar de 67% din membri pentru a decide acceptarea sau eliminarea unui membri. Pretentia de a apartine unei singure companii va fi atunci impusa de grup.

Exemple de “skeleton code” pentru o DAO


Exista 3 tipuri de tranzactii:

[0,k]pentru a inregistra un vot pentru schimbarea codului
[1,k,L,v0,v1...vn]pentru a inregistra o shimbare de cod k in favoarea setarii memoriei incepand cu locatia L pana la v0, v1 ... vn
[2,k]pentru a finaliza o schimbare de cod
Tineti minte ca acest design e bazat pe un model aleatoriu de adrese si hashes, pentru a asigura integritatea datelor; contractul va fi corupt in vreun fel dupa 2^128 utilizari, dar acest numar este acceptabil, deoarece un astfel de nivel de uzare este improbabil in viitorul apropiat.2^255 este folosit ca un numar “magic” pentru a stoca numarul total de membri, si apartenenta este denotata cu un 1 la adresa membrului. Ultimele 3 randuri din contract sunt folosite pentru a adauga C ca un prim membru; din acel moment, va fi responsabilitatea lui C sa foloseasca protocolul democratic de a schimba codul pentru activitati precum adaugarea membrilor si bootstrap.

    if tx.value < tx.basefee * 200:
        stop
    if contract.storage[tx.sender] == 0:
        stop
    k = sha3(32,tx.data[1])
    if tx.data[0] == 0:
        if contract.storage[k + tx.sender] == 0:
            contract.storage[k + tx.sender] = 1
            contract.storage[k] += 1
    else if tx.data[0] == 1:
        if tx.value <= tx.datan * block.basefee * 200 or contract.storage[k]:
            stop
        i = 2
        while i < tx.datan:
            contract.storage[k + i] = tx.data[i]
            i = i + 1
        contract.storage[k] = 1
        contract.storage[k+1] = tx.datan
    else if tx.data[0] == 2:
        if contract.storage[k] >= contract.storage[2 ^ 255] * 2 / 3:
            if tx.value <= tx.datan * block.basefee * 200:
                stop
            i = 3
            L = contract.storage[k+1]
            loc = contract.storage[k+2]
            while i < L:
                contract.storage[loc+i-3] = tx.data[i]
                i = i + 1
    if contract.storage[2 ^ 255 + 1] == 0:
        contract.storage[2 ^ 255 + 1] = 1
        contract.storage[C] = 1
Aceasta implementeaza modelul egalitar DAO unde membrii au un numar egal de actiuni. Se poate adapta la un “shareholder model” prin introducerea numarului de actiuni detinute de fiecare membru, si furnizarea unui mod simplu de transfer de actiuni.


DAO si DAC au fost un punct de interes pentru utilizatorii de cryptocurrency ca o viitoare forma de organizare economica, si suntem incantati de potentialul oferit de DAO. Pe termen lung, chiar fondul Ethereum intentioneaza sa faca o tranzitie, devenind un DAO autonom.

.


Alte Aplicatii

1) Savings wallets. Sa presupunem ca Alice vrea sa aiba conturile in siguranta, dar e ingrijorata ca isi va pierde (sau cineva ii va fura) codul. Depune ether intr-un contract cu Bob, o banca, dupa cum urmeaza: Alice poate retrage maxim 1% din fonduri pe zi, Alice impreuna cu Bob pot retrage totul, si Bob de unul singur poate retrage 0.05% din fonduri. In mod normal, 1% pe zi este destul pentru Alice, si daca vrea sa retraga mai mult, poate cere ajutorul lui Bob. Daca codul lui Alice este folosit ilicit de altcineva, apeleaza la Bob pentru a muta fondurile pe un nou contract. Daca ea isi pierde codul, Bob va retrage fondurile in cele din urma. Daca Bob este rau intentionat, Alice oricum poate retrage de 20 de ori mai repede decat el.

2) Crop insurance.Se poate face usor un  “financial derivatives contract” folosind date de prognoza meteo in locul unui index de preturi. Daca un fermier in Iowa cumpara derivative achitate invers proportional bazat pe precipitatiile din Iowa si vine seceta, fermierul va primi automat bani; daca precipitatiile sunt abundente, fermierul va fi fericit ca recolta va prospera.

3) Un decentrally managed data feed, care foloseste un sistem de votare proof-of-stake pentru a furniza o medie intre opiniile tuturor asupra pretului unui produs, vremea sau alte date relevante.

4) Smart multisignature escrow. Bitcoin accepta contracte de tranzactii multisignature unde, de exemplu, 3 din 5 keys pot folosi fondurile. Ethereum permite mai multa granularitate; de exemplu, 4 din 5 pot cheltui toate fondurile, 3 din 5 pot folosi pana la 10% pe zi si 2 din 5 pot folosi pana la 0.5% pe zi. In plus, Ethereum multisig este asincron – doi membri isi pot inregistra semnaturile pe blockchain la perioade diferite, si ultima semnatura va trimite automat tranzactia.

5) Peer-to-peer gambling. Orice numar de protocoale de gambling, precum Cyberdice, al lui Frank Stajano si Richard Clayton, poate fi implementat pe blockchain-ul Ethereum. Cel mai simplu gambling protocol este de fapt un contract de diferenta pe urmatorul block hash. De aici, servicii de gambling precum SatoshiDice pot fi imitate pe blockchain prin crearea unui contract unic pe pariu sau prin folosirea unui contract quasi-centralizat.

6) O piata de capital on-chain pe scara larga. Prediction markets (piete de predictie) sunt de asemenea usor de implementat ca si consecinta triviala.

7O piata on-chain descentralizata , care foloseste   un sistem bazat pe identitate si reputatie.

8) Dropbox Decentralizat. O metoda este aceea de a cripta un fisier, a organiza o schema Merkel, a depune o radacina Merkle intr-un contract alaturi de o anume suma de ether, si a distribui fisierul pe o retea secundara. In fiecare zi, contractul va alege o ramura aleatorie din acea schema Merkel in functie de block hash, si va acorda un numar X de ether primului nod care furnizeaza ramura catre contract, astfel incurajand nodurile sa stocheze datele pe termen lung, pentru a revendica premiul. Daca cineva incearca sa download-eze o portiune a fisierului, poate folosi un contract in stilul micropayment channel pentru a downloada fisierul de la cateva noduri, cate un block pe rand.


Cum functioneaza contractele?

Un contract care face tranzactii este codat dupa cum urmeaza:

    [
        nonce,
        '',
        value,
        [
            data item 0,
            data item 1,
            ...
        ],
        v,
        r,
        s
    ]
Itemii data vor fi, in majoritatea cazurilor, coduri script(mai mult despre acest lucru , mai jos). Validarea tranzactiei crearii contractului se desfasoara dupa cum urmeaza:

Deserializeaza tranzactia si extrage adresa expeditoare din semnatura sa.
Calculeaza taxa de tranzactie ca NEWCONTRACTFEE plus taxe de depozitare pentru cod.Verifica daca balanta creatorului este cel putin egala cu valoarea tranzactiei plus taxa. Daca nu, iese.
Ia ultimii 20 bytes ai sha3 hash ai codarii RLP a tranzactiei, realizand contractul. Daca un cont cu aceeasi adresa exista deja, se paraseste operatiunea. Altfel, creati contractul la acea adresa.
Se copiaza itemul data i pentru a depozita slotul i in contract pentru toti i in [0 ... n-1]unde n este numarul itemilor data din tranzactie, si initializeaza contractul cu valoarea tranzactiei ca fiind valoarea contractului. Se scade valoarea si taxa din balanta creatorului.

Specificatii de limbaj

Limbajul de scripting al contractului este un hibrid din limbajul de asamblare si din limbajul stack-based al Bitcoin-ului, mentinand un indicator de index care de obicei creste cu unu dupa fiecare operatie si proceseaza continuu operatia gasita la indicatorul de index curent. Toate opcodes sunt numere in gama[0 ... 63]; etichete mai departe in descrierea sa ca STOP, EXTRO si BALANCE se refera la valori specifice care sunt definite mai departe. Limbajul scripting are acces la tri feluri de memorie:

Stack - o forma temporara de depozitare care este resetata pentru a goli lista de fiecare data cand un contract este executat. Operatiile , de obicei, adauga sau elimina valori la/de la varful stack-ului, astfel lungimea totala a unui stack va creste sau va scadea pe parcursul executarii programului.
Memory - un stoc temporar de valori/keys care este resetat la continutul tuturor zerourilor de fiecare data cand un contract este executat.Keys si valori in memorie sunt numere intregi in gama [0 ... 2^256-1]
Storage - un stoc persistent de valori/keys care initial este setat sa contina toate zerourile, exceptand anumite coduri script inserate la inceput cand contractul este creat. Valorile si keys aflate in storage sunt numere integrale aflate in gama [0 ... 2^256-1]
Oricand o tranzactie este trimisa catre un contract, contractul va executa codul sau scripting.Pasii precisi care sunt facuti atunci cand un contract primeste o tranzactie sunt urmatorii:

Interpretarea Script-ului Contractelor 


Balanta ether a contractului creste odata cu suma trimisa.
Indicatorul de index este setat la zero, si STEPCOUNT = 0
Repeta la nesfarsit:
daca comanda de la indicatorul de index este STOP, invalida sau mai mare de 63, iesiti din bucla
setati MINERFEE = 0, VOIDFEE = 0
setati STEPCOUNT <- STEPCOUNT + 1
daca STEPCOUNT > 16, set MINERFEE <- MINERFEE + STEPFEE
verificati daca comanda este LOAD sau STORE. Daca este asa, setati MINERFEE <- MINERFEE + DATAFEE
vedeti daca comanda va modifica un camp de depozitare, sa spunem KEY din OLDVALUE in NEWVALUE. Lasati F(K,V)fie 0daca V == 0 altfel (len(K) + len(V)) * STORAGEFEE in bytes. Set VOIDFEE <- VOIDFEE - F(KEY,OLDVALUE) + F(KEY,NEWVALUE). Calcularea len(K) ignora leading zero bytes.
vedeti daca comanda este EXTRO sau BALANCE. Daca este asa, setati MINERFEE <- MINERFEE + EXTROFEE
verificati daca comanda este o operatie crypto.Daca este asa, setati MINERFEE <- MINERFEE + CRYPTOFEE
daca MINERFEE + VOIDFEE > CONTRACT.BALANCE, OPRITI-VA si iesiti din bucla
scadeti MINERFEE din balanta contractului si adaugati MINERFEE la un running counter care va fi daugat la balanta minerului odata ce toate tranzactiile sunt analizate.
setati DELTA = max(-CONTRACT.STORAGE_DEPOSIT,VOIDFEE) si CONTRACT.BALANCE <- CONTRACT.BALANCE - DELTA si CONTRACT.STORAGE_DEPOSIT <- CONTRACT.STORAGE_DEPOSIT + DELTA. Retineti ca DELTA poate fi pozitiva sau negativa; singura restrictie este aceea ca depozitul contractului nu poate merge sub zero.
rulati comanda
daca comanda nu a iesit cu o eroare, updatati indicatorul de index si intoarceti-va la inceputul buclei.Daca contractul a iesit cu o eroare, iesiti din bucla. Retineti ca un contract existent cu o eroare nu face tranzactia sau block-ul invalide; pur si simplu inseamna ca execturia contractului s-a oprit la mijlocul procesului.
In urmatoarele descrieri, S[-1], S[-2], etc reprezinta cei mai importanti, descrescator, itemi ai stack-ului. Opcode-urile individuale sunt definite dupa cum urmeaza:

(0) STOP - opreste executia
(1) ADD - pops two items and pushes S[-2] + S[-1] mod 2^256
(2) MUL - pops two items and pushes S[-2] * S[-1] mod 2^256
(3) SUB - pops two items and pushes S[-2] - S[-1] mod 2^256
(4) DIV - pops two items and pushes floor(S[-2] / S[-1]). If S[-1] = 0, opreste executia.
(5) SDIV - pops two items and pushes floor(S[-2] / S[-1]), but treating values above 2^255 - 1 as negative (ie. x -> 2^256 - x). If S[-1] = 0, halts execution.
(6) MOD - pops two items and pushes S[-2] mod S[-1]. If S[-1] = 0, halts execution.
(7) SMOD - pops two items and pushes S[-2] mod S[-1], but treating values above 2^255 - 1 as negative (ie. x -> 2^256 - x). If S[-1] = 0, halts execution.
(8) EXP - pops two items and pushes S[-2] ^ S[-1] mod 2^256
(9) NEG - pops one item and pushes 2^256 - S[-1]
(10) LT - pops two items and pushes 1 if S[-2] < S[-1] else 0
(11) LE - pops two items and pushes 1 if S[-2] <= S[-1] else 0
(12) GT - pops two items and pushes 1 if S[-2] > S[-1] else 0
(13) GE - pops two items and pushes 1 if S[-2] >= S[-1] else 0
(14) EQ - pops two items and pushes 1 if S[-2] == S[-1] else 0
(15) NOT - pops one item and pushes 1 if S[-1] == 0 else 0
(16) MYADDRESS - pushes the contract's address as a number
(17) TXSENDER - pushes the transaction sender's address as a number
(18) TXVALUE - pushes the transaction value
(19) TXDATAN - pushes the number of data items
(20) TXDATA - pops one item and pushes data item S[-1], or zero if index out of range
(21) BLK_PREVHASH - pushes the hash of the previous block (NOT the current one since that's impossible!)
(22) BLK_COINBASE - pushes the coinbase of the current block
(23) BLK_TIMESTAMP - pushes the timestamp of the current block
(24) BLK_NUMBER - pushes the current block number
(25) BLK_DIFFICULTY - pushes the difficulty of the current block
(26) BLK_NONCE - pushes the nonce of the current block
(27) BASEFEE - pushes the base fee (x as defined in the fee section below)
(32) SHA256 - pops two items, and then constructs a string by taking the ceil(S[-1] / 32) items in memory from index S[-2] to (S[-2] + ceil(S[-1] / 32) - 1) mod 2^256, prepending zero bytes to each one if necessary to get them to 32 bytes, and takes the last S[-1] bytes. Pushes the SHA256 hash of the string
(33) RIPEMD160 - works just like SHA256 but with the RIPEMD-160 hash
(34) ECMUL - pops three items. If (S[-2],S[-1]) are a valid point in secp256k1, including both coordinates being less than P, pushes (S[-2],S[-1]) * S[-3], using (0,0) as the point at infinity. Otherwise, pushes (2^256 - 1, 2^256 - 1). Note that there are no restrictions on S[-3]
(35) ECADD - pops four items and pushes (S[-4],S[-3]) + (S[-2],S[-1]) if both points are valid, otherwise (2^256 - 1,2^256 - 1)
(36) ECSIGN - pops two items and pushes (v,r,s) as the Electrum-style RFC6979 deterministic signature of message hash S[-1] with private key S[-2] mod N with 0 <= v <= 3
(37) ECRECOVER - pops four items and pushes (x,y) as the public key from the signature (S[-3],S[-2],S[-1]) of message hash S[-4]. If the signature has invalid v,r,s values (ie. v not in [27,28], r not in [0,P], s not in [0,N]), return (2^256 - 1,2^256 - 1)
(38) ECVALID - pops two items and pushes 1 if (S[-2],S[-1]) is a valid secp256k1 point (including (0,0)) else 0
(39) SHA3 - works just like SHA256 but with the SHA3 hash, 256 bit version
(48) PUSH - pushes the item in memory at the index pointer + 1, and advances the index pointer by 2.
(49) POP - pops one item.
(50) DUP - pushes S[-1] to the stack.
(51) SWAP - pops two items and pushes S[-1] then S[-2]
(52) MLOAD - pops two items and sets the item in memory at index S[-1] to S[-2]
(53) MSTORE - pops two items and sets the item in memory at index S[-1] to S[-2]
(54) SLOAD - pops two items and sets the item in storage at index S[-1] to S[-2]
(55) SSTORE - pops two items and sets the item in storage at index S[-1] to S[-2]
(56) JMP - pops one item and sets the index pointer to S[-1]
(57) JMPI - pops two items and sets the index pointer to S[-2] only if S[-1] is nonzero
(58) IND - pushes the index pointer
(59) EXTRO - pops two items and pushes memory index S[-2] of contract S[-1]
(60) BALANCE - pops one item and pushes balance of the account with that address, or zero if the address is invalid
(61) MKTX - pops four items and initializes a transaction to send S[-2] ether to S[-1] with S[-3] data items. Takes items in memory from index S[-4] to index (S[-4] + S[-3] - 1) mod 2^256 as the transaction's data items.
(63) SUICIDE - pops one item, destroys the contract and clears all storage, sending the entire balance plus the contract deposit to the account at S[-1]
Dupa cum am mentionat si mai sus, intentia nu este ca oamenii sa scrie script-uri direct in codul script al Ethereum, ci, vom pune la dispozitie compilatoare pentru a genera ES din limbaje de un nivel mai inalt.Primul limbaj suportat va fi cel mai probabil limbajul simplu C-like utilizat in descrierile de mai sus, iar al doilea va fi mai un limbaj de prima clasa mai complet cu suport pentru matrice si pentru siruri de lungimi arbitrare.Compilarea limbajului C este destul de simpla in ceea ce priveste eficienta compilatoarelor: variabilelor le pot fi repartizate in index de memorie, si formarea unei expresii aritmetice implica, esential, convertirea la reverse Polish notation (eg. (3 + 5) * (x + y) -> PUSH 3 PUSH 5 ADD PUSH 0 MLOAD PUSH 1 MLOAD ADD MUL). Limbajul de prima clasa este mai implicat datorita definirii variabile a domeniului, dar problema este cu toate acestea usor de manuit. Solutia posibila va fi mentinerea unei liste linked a frame-urilor stack in memorie, dand fiecarui stack frame N sloturi de memorie unde N este numarul total de nume de variabile distincte in program.Accesul varialilelor va consta in cautarea in lista stck frame pana cand un frame va contine un indicator spre variabila, copiind indicatorul in varful stack-ului pentru scopuri de memorare, si returnarea valorii la indicator.Totusi, acestea sunt griji pe termen lung; compilarea este separata de protocolul in sine, si, astfel, va fi pobila continuarea cercetarii strategiilor de compilare si dupa ce reteaua este in functiune.


Taxe

In Bitcoin, nu exista taxe obligatorii pe tranzactie. Tranzactiile pot include taxe platite catre mineri, si acestia pot decide ce taxe vor accepta. In Bitcoin, un astfel de mecanism este imperfect; nevoia unui block size limit de 1 MB pe langa mecanismul de taxare ilustreaza acest fapt. In Ethereum, datorita Turing-completeness, un sistem de taxare voluntara ar fi catastrofal. In loc, Ethereum va avea un sistem de taxe obligatorii, care includ taxele de tranzactie si 6 taxe pentru calculele contractului.

Taxele sunt setate astfel:

TXFEE (100x) - taxa pentru a trimite o tranzactie
NEWCONTRACTFEE (100x) - ftaxa pentru crearea unui nou contract; nu include taxa de stocare a fiecarui item in script code
STEPFEE (1x) - taxa pentru fiecare pas computational ce urmeaza dupa primii 16 in executia contractului
STORAGEFEE (5x) - taxa per-byte pentru a adauga la stocarea contractului. Taxa de stocare este singura taxa care nu este platita catre un miner, si e inapoiata cand spatiul de stocare folosit de un contract e redus sau eliminat.
DATAFEE (20x) - taxa pentru accesarea sau setarea memoriei unui contract, in interiorul acelui contract
EXTROFEE (40x) - taxa pentru accesarea memoriei dintr-un alt contract in alt contract
CRYPTOFEE (20x) - taxa pentru folosirea oricarei operatiuni criptografice
Coeficientii vor fi revizuiti pe masura ce creste numarul datelor despre costul computational al fiecarei operatii. Partea cea mai dificila va fi setarea valoarea lui x.In prezent avem in vedere doua solutii principale:

Faceti x proportional cu radacina patrata a dificultatii, astfel ca x = floor(10^21 / floor(difficulty ^ 0.5)). Aceasta ajusteaza automat taxele pe masura ce valoarea ether creste, si scade taxele pe masura ce calculatoarele devin mai performante, datorita legii lui Moore.
Folositi votarea proof-of-stake pentru a stabili taxele. Teoretic, partile interesate nu beneficiaza direct de cresterea sau diminuarea taxelor, deci stimulentele ar fi luarea deciziilor pentru a maximiza valoarea retelei..
O solutie hibrida este de asemenea posibila, prin folosirea votarii proof-of-stake, dar avand mecanismul inversului radacinii patrate ca metoda initiala.


Concluzie

Filosofia de baza a protocolului Ethereum este in multe feluri diferita de cea din spatele multor feluri de cryptocurrency actuale. Alte cryptocurrencies urmaresc sa creasca complexitatea si sa mareasca numarul de "functii"; Ethereum, pe de alta parte, elimina din functii. Protocolul nu "suporta" tranzactii multisignature, multiple input-uri si output-uri, hash codes, lock times sau multe alte functii oferite chiar si de Bitcoin. In schimb, complexitatea vine dintr-un scripting language Turing universal, care poate fi folosit pentru a crea orice functie care poate fi descrisa matematic prin mecanismul contractului. Ca rezultat, avem un protocol cu un potential unic; departe de a fi un protocol limitat, cu un singur scop, intentionat pentru un numar specific de aplicatii in stocarea de date, gambling sau finante,  Ethereum este open-ended in esenta, si avem motive sa credem ca este foarte potrivit pentru a servi ca fundatie pentru un numar mare de protocoale financiare si non-financiare in anii ce vor urma.


Referinte si Documentare

Colored coins whitepaper: https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit
Mastercoin whitepaper: https://github.com/mastercoin-MSC/spec
Decentralized autonomous corporations, Bitcoin Magazine: http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/
Smart property: https://en.bitcoin.it/wiki/Smart_Property
Smart contracts: https://en.bitcoin.it/wiki/Contracts
Simplified payment verification: https://en.bitcoin.it/wiki/Scalability#Simplifiedpaymentverification
Merkle trees: http://en.wikipedia.org/wiki/Merkle_tree
Patricia trees: http://en.wikipedia.org/wiki/Patricia_tree
Bitcoin whitepaper: http://bitcoin.org/bitcoin.pdf
GHOST: http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf
StorJ and Autonomous Agents, Jeff Garzik: http://garzikrants.blogspot.ca/2013/01/storj-and-bitcoin-autonomous-agents.html
Mike Hearn on Smart Property at Turing Festival: http://www.youtube.com/watch?v=Pu4PAMFPo5Y
Ethereum RLP: http://wiki.ethereum.org/index.php/RLP
Ethereum Merkle Patricia trees: http://wiki.ethereum.org/index.php/Patricia_Tree
Ethereum Dagger: http://wiki.ethereum.org/index.php/Dagger
Ethereum C-like language: http://wiki.ethereum.org/index.php/CLL
Ethereum Slasher: http://blog.ethereum.org/?p=39/slasher-a-punitive-proof-of-stake-algorithm
Scrypt parameters: https://litecoin.info/User:Iddo/ComparisonbetweenLitecoinandBitcoin#SHA256miningvsscryptmining
Litecoin ASICs: https://axablends.com/merchants-accepting-bitcoin/litecoin-discussion/litecoin-scrypt-asic-miners/