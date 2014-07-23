### Una Piattaforma di Nuova Generazione per i Contratti Intelligenti e le Applicazioni Decentralizzate

Quando Satoshi Nakamoto attivò per primo, nel Gennaio 2009, la blockchain del Bitcoin, stava introducendo due concetti radicali e altamente non testati fino ad allora. Il primo è il "bitcoin", una moneta online peer-to-peer decentralizzata che mantiene un valore senza nessun supporto, [valore intrinseco](http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/) o emittente centrale. Finora, il "Bitcoin" come unità monetaria ha catturato la maggior parte dell'attenzione del pubblico, sia in termini di aspetti politici di una moneta senza una banca centrale che per l' estrema volatilità del prezzo verso l'alto e verso il basso. Comunque c'è anche un'altra, ugualmente importante, parte del grandioso esperimento di Satoshi: il concetto di una blockchain basata sul  proof-of-work che permette un consenso pubblico sul sistema delle transazioni. Il Bitcoin come un'applicazione può essere descritto come un sistema first-to-file: se un'entità ha 50 BTC, e simultaneamente invia gli stessi 50 BTC ad A e a B, solamente la transazione che viene confermata per prima sarà processata. Non c'è nessun intrinseco modo di determinare quale delle due transazioni è avvenuta prima, e per decenni questo ha ostacolato lo sviluppo delle monete digitali decentralizzate. La blockchain di Satoshi era la prima soluzione decentralizzata. Ed ora, l'attenzione sta iniziando rapidamente a spostarsi verso questa seconda parte della tecnologia Bitcoin, e come il concetto di blockchain possa essere utilizzato per qualcosa di più che il denaro.

Le Applicazioni generalmente citate includono l'utilizzo di risorse digitali sulla blockchain per rappresentare valute personalizzate e strumenti finanziari (["monete colorate"](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit)), la proprietà di un dispositivo fisico sottostante (["proprietà intelligente"](htups://en.bitcoin.it/wiki/Smart_Property)), assets non fungibili, quali nomi di dominio ("Namecoin") così come le più avanzate applicazioni tipo un exchange decentrallizzato, derivati finanziari, il gioco d'azzardo peer-to-peer ed il sistema di identità e di reputazione sulla blockchain. Un'altra importante area di indagine sono i "contratti intelligenti" - sistemi che automaticamente trasferiscono assets digitali in accordo con regole pre-specificate. Per esempio, si potrebbe avere un contratto di tesoreria nella forma "A può trasferire a X alcune unità di moneta al giorno, B può trasferire a Y alcune unità di moneta al giorno, A e B insieme non possono trasferire niente, ed A può precludere l' "abilità di trasferire" di B. La logica estensione di questo è [organizzazioni autonome decentralizzate](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/) (DAOs) - contratti intelligenti a lungo termine che gestiscono assets e codificano lo statuto di un'intera organizzazione. Quello che Ethereum intende garantire è una blockchain con una linguaggio di programmazione di Turing, completo e costruito al suo interno, che può essere usato per creare "contratti" e per codificare le funzioni arbitrarie di transizione, permettendo agli utenti di creare uno dei sistemi sopra descritti, così come molti altri che non abbiamo ancora immaginato, semplicemente scrivendo la logica in poche righe di codice.

### Tavola dei Contenuti

* [Storia](#history)
    * [Bitcoin come un Sitema di Transizione di Stato](#bitcoin-as-a-state-transition-system)
    * [Mining](#mining)
    * [Merkle Trees](#merkle-trees)
    * [Applicazioni Alternative della blockchain](#alternative-blockchain-applications)
    * [Scripting](#scripting)
* [Ethereum](#ethereum)
    * [Portafogli Ethereum](#ethereum-accounts)
    * [Messaggi e Transazioni](#messages-and-transactions)
    * [Ethereum come un Sitema di Transizione di Stato](#ethereum-state-transition-function)
    * [Esecuzione del Codice](#code-execution)
    * [Blockchain e Mining](#blockchain-and-mining)
* [Applicazioni](#applications)
    * [Sistemi di Prova](#token-systems)
    * [Derivati Finanziari](#financial-derivatives-and-stable-value-currencies)
    * [Sistema di Identità e Reputazione](#identity-and-reputation-systems)
    * [Storage Decentralizzata dei File](#decentralized-file-storage)
    * [Organizzazioni Decentralizzate Autonome](#decentralized-autonomous-organizations)
    * [Ulteriori Applicazioni](#further-applications)
* [Miscellanea e Relazioni](#miscellanea-and-concerns)
    * [Implementazione GHOST Modificata](#modified-ghost-implementation)
    * [Commissioni](#fees)
    * [Calcolo e Completezza di Turing](#computation-and-turing-completeness)
    * [Moneta ed Emissione](#currency-and-issuance)
    * [Centralizzazione del Mining](#mining-centralization)
    * [Scalabilità](#scalability)
* [Conclusione](#conclusion)
* [Referenze ed Ulteriori Letture](#references-and-further-reading)

## Introduzione al Bitcoin ed ai Concetti Esistenti

### Storia

Da decenni sta circolando l'idea di una moneta digitale decentralizzata, così come delle applicazioni alternative come i registri di proprietà. I protocolli anonimi di moneta digitale degli anni '80 e degli anni '90, dipendenti soprattutto su una crittografia primitiva conosciuta come Chaumian blinding, furono forniti da una moneta con un alto tasso di privacy, tuttavia i suddetti protocolli fallirono principalmente per non essere riusciti a guadagnare terreno a causa della loro dipendenza da un intermediario centralizzato. Nel 1998, Wei Dai's [b-money](http://www.weidai.com/bmoney.txt) divenne il primo progetto per l'introduzione dell'idea di creare moneta attraverso la soluzione di puzzle computazionali così come il consenso decentralizzato, ma il progetto era scarno di dettagli su come questo consenso decentralizzato potesse essere concretamente attualizzato. Nel 2005, Hal Finney introdusse il concetto del "[proofs of work riutilizzabile](http://www.finney.org/~hal/rpow/)", un sistema che utilizza le idee provenienti dalla b-moneta insieme ai puzzle computazionali Hashcash di Adam Back per creare un concetto di criptomoneta, ma ancora una volta non riuscii ad ottenere l'idea perché si basava su un calcolo computazionale garantito come backend.

Poichè la moneta è un'applicazione first-to-file, dove l'ordine delle transazioni è spesso di cruciale importanza, monete decentralizzate richiedono una soluzione al consenso decentralizzato. L'ostacolo principale che tutti i protocolli di valuta pre-Bitcoin hanno affrontato consiste nel fatto che, mentre c'era stata moltissima ricerca, per molti anni, sulla creazione di un consenso Byzantine-fault-tolerant condiviso da più parti, dove tutti i protocolli descritti in precedenza risolvevano soltanto la metà del problema. I protocolli supponevano che nel sistema fosse conosciuto, e prodotto margini di sicurezza della forma "se N parti partecipano, poi il sistema può tollerare fino a  N/4 cattivi attori". Tuttavia il problema è che in una regolazione anonima i margini di sicurezza sono vulnerabili da attacchi sibilla, dove un singolo attaccante può creare migliaia di nodi simulati su un server o su una botnet ed usare questi nodi per assicurarsi una quota di maggioranza.

L'innovazione fornita da Satoshi è l'idea di riuscire a combinare un protocollo molto semplice di consenso decentralizzato, basato su dei nodi su cui avvengono le transazioni, che creano una sempre più crescente blockchain attraverso la creazione di "blocchi" ogni 10 minuti, con il proof of work come meccanimso attraverso cui i nodi guadagnano il diritto di partecipare al sistema. Mentre i nodi con una grande quantità di potenza di calcolo hanno proporzionalmente maggiore influenza, raggiungere più potenza computazionale rispetto l'intero network è più difficile rispetto al simulare milioni di nodi. Nonostante la crudezza e la semplicità del modello blockchain del Bitcoin, esso ha dimostrato di essere abbastanza valido, e nel corso dei successivi cinque anni sarebbe diventato il fondamento di oltre duecento monete e protocolli di tutto il mondo.

### Il Bitcoin come un Sistema di Transizione di Stato

![statetransition.png](http://vitalik.ca/files/statetransition.png?2)

Da un punto di vista tecnico, il libro mastro del Bitcoin può essere pensato come un sistema di transizione di stato, dove c'è uno "stato" consistente nella proprietà dello status di tutti i bitcoins esistenti e "la funzione di transizione di stato" che riceve uno stato ed una transizione e trasmette un nuovo stato che ne costituisce il risultato. Nel sistema bancario tradizionale, per esempio, lo stato è il documento costituente il saldo, una transazione è una richiesta di movimentare $X da A a B, e la funzione di transizione di stato sottrae un valore nel conto corrente di A equivalente $X ed incrementa il valore di $X nel conto corrente bancario di B. Se nel conto corrente di A ci sono meno che $X nel primo posto, la funzione di transizione di stato segnala un errore. Quindi, si può formalmente definire:

    APPLY(S,TX) -> S' or ERROR

Nel sistema bancario definito in precedenza:

    APPLY({ Alice: $50, Bob: $50 },"send $20 from Alice to Bob") = { Alice: $30, Bob: $70 }

Ma:
    
    APPLY({ Alice: $50, Bob: $50 },"send $70 from Alice to Bob") = ERROR

Lo "stato" nel Bitcoin è la raccolta di tutte le monete (tecnicamente, "transazioni in uscita non spese" oUTXO) che sono state effettuate ma non ancora spese, con ogni UTXO che ha una denomoninazione ed un proprietario (definito da un indirizzo da 20-byte che è essenzialmente una chiave crittografica pubblica<sup>[1]</sup>). Una transazione contiene uno o più inputs, con ogni input che contiente un riferimento ad un UTXO esistente ed una firma crittografica prodotta da una chiave privata associata all'indirizzo del proprietario, ed uno o più outputs, con ogni outpout contenente un nuovo UTXO che deve essere aggiunto allo stato.

1. Per ogni input in `TX`:
    * Se il riferimento UTXO non è in `S`, si ha un errore.
    * Se la firma provvista non combacia con il proprietario del UTXO, si ha un errore.
2. Se la somma dei valori di tutti gli input UTXO è inferiore alla somma dei valori di tutti gli outpout UTXO, si ha un errore.
3. Si ha `S` con tutti gli input UTXO meno tutti gli outpout UTXO aggiunti.

La prima metà del primo step previene che ai mittenti delle transazioni di spendere monete che non esistono, la seconda metà del primo step previene ai mittenti delle transazioni di spendere monete di altre persone, ed il secondo step fa rispettare la conservazione del valore. Al fine di utilizzare questo per il pagamento, il protocollo è il seguente. Supponiamo che Alice vuole inviare 11.7 BTC a Bob. Per primo, Alice guarderà per un set di UTXO che lei possiede che ammonta fino ad almeno 11.7 BTC. Realisticamente, Alice non sarà in grado di ottenere esattamente 11.7 BTC; si può dire che il valore più piccolo che può ottenere è 6+4+2=12. Lei poi crea una transazione con quei tre inputs ed i due outputs. Il primo output sarà 11.7 BTC con l'indirizzo che appartiene a Bob, ed il secondo output sarà il rimanente 0.3 BTC "scambiato", con il proprietario essendo Alice stessa.