---
name: Introduzione allo sviluppo su Ethereum
category: 
---

_traduzione in Italiano da [Dapps for beginners blog => Introduction to development on Ethereum](https://dappsforbeginners.wordpress.com/tutorials/introduction-to-development-on-ethereum/)_   

# Ethereum – una rete di consenso decentralizzato

Per la maggior parte degli sviluppatori imparare come utilizzare una nuova piattaforma, o un linguaggio di programmazione, o un nuovo framework rappresenta un compito familiare, probabilmente ripetuto dozzine di volte durante la loro carriera. Molto diverso e’ l’imparare a sviluppare per un paradigma completamente differente. La rete di consenso decentralizzato, la blockchain, e la sua implementazione piu’ conosciuta ‘bitcoin’ non sono ben compresi neppure dalla comunita’ degli sviluppatori e le sfumature di come questa tecnologia sia radicalmente diversa da cio’ che abbiamo utilizzato fino ad ora sono certamente ignote alla maggior parte del pubblico. 

Tenendo cio’ a mente, prima di procedere a costruire la nostra prima decentralised apps indicheremo l'insieme di quelle tecnologie principali che servono a creare un network di consenso decentralizzato, e la teoria dei giochi che fa uso di queste tecnologie per creare un network. 

## Tecnologie principali

**[Crittografia a chiave pubblica (anche deta crittografia asimmetrica)](http://it.wikipedia.org/wiki/Crittografia_asimmetrica)**

La crittografia asimmetrica e’ quell'insieme di metodi di criptazione che richiede la creazione di due chiavi separate; la “chiave privata” - che e’ nota solo al proprietario - e la “chiave pubblica” che e’ nota a chiunque. Questo tipo di crittografia possiede una serie di utili attributi, il primo e’ che chiunque e’ in grado di criptare dati con una chiave pubblica che possono essere decriptati solamente dalla chiave privata. 

Il secondo e’ l’abilita’ per il possessore della chiave privata di “firmare” qualsiasi informazione utilizzando la propria chiave privata, affinche' chiunque abbia la chiave pubblica possa verificare la firma, ma senza che si rilascino informazioni sulla chiave privata. Questo secondo attributo e’ usato per il sistema di account in un network di consenso decentralizzato, e sta alla base dell’invio delle transazioni. 

**[Funzione crittografica di hash](http://it.wikipedia.org/wiki/Funzione_crittografica_di_hash)**

Una funzione di hash e’ una funzione che prende un segmento di informazione di qualsiasi dimensione e lo mappa su un altro segmento di dimensione fissa, e.g. un file di 1MB o di 500kb - se vengono sottoposti ad una funzione di hash - producono “hashes” di 128 bit di dimensione ciascuno. Una funzione crittografica di hash assolve a questo compito e lo fa anche rispettando tre importanti requisiti: 1) non viene fornita nessuna informazione su che tipo di dati abbiano prodotto l’hash (ovvero, la funzione e’ irreversibile), 2) una piccola variazione nel dato di input produce un hash estremamente differente, in modo che l’hash non puo’ essere calcolato se non utilizzando la funzione di hash (ovvero, niente scorciatoie!!), 3) c’e’ una probabilita’ estremamente bassa che due dati di input diversi producano lo stesso hash.

**[Il networking peer-to-peer](http://it.wikipedia.org/wiki/Peer-to-peer)**

A differenza del modello “Client-Server”, il modello “peer-to-peer” consiste di reti di computer connessi direttamente l’un l’altro, senza che questi inoltrino alcuna richiesta ad un server centrale. Tutti i computer che fanno parte della rete sono considerati “peers” (pari, in inglese) e mantengono l’un l’altro una posizione paritaria all’interno della rete stessa. Le reti "peer-to-peer" generalmente si basano sull’altruismo, ovvero ogni computer fornisce alla rete almeno lo stesso ammontare di risorse che trae dalla stessa. 

##Tecnologie della cripto-economia

**[La Blockchain](http://it.wikipedia.org/wiki/Bitcoin#Tecnologia)**

La Blockchain in tutte le sue incarnazioni e’ un tipo di database progettato specificatamente per essere utilizzato in una rete di consenso decentralizzato. Puo’ contenere qualsiasi informazione, e puo’ stabilire delle regole sulla maniera in cui l’informazione viene aggiornata. La sua caratteristica principale e’ che viene aggiornata a spezzoni chiamati “blocchi” che sono “incatenati” usando degli hashes del contenuto dei blocchi precedenti. Una blockchain contiene non solo l’informazione contenuta attualmente nel database, ma anche ogni cambiamento apportato al database nel corso della sua storia. Dati lo stato e le transazioni, la blockchain rappresenta un database con una storia completa che non puo’ essere alterata senza alterare ciascuno dei blocchi successivi. Una chiave privata crittografica firma ogni “transazione” o richiesta di cambiare lo stato del database, e la firma stessa e’ custodita nella blockchain.

**[La Proof-of-work](http://it.wikipedia.org/wiki/Proof-of-work)**

Pensato inizialmente come un sistema per prevenire lo spam, il sistema di proof of work e’ un semplice metodo per assicurarsi che tu abbia ***probabilmente*** compiuto una grande quantita’ di operazioni matematiche. E’ implementato nella maggior parte dei casi utilizzando una funzione di hash crittografica; dato un segmento di informazione arbitrario, (ad esempio come una lista di transazioni e l’intestazione di un blocco) devi trovare un secondo segmento di infromazione che, unito al primo, produce un hash che ha determinate caratteristiche (come ad esempio un certo numero di zeri consecutivi). Dato che e’ impossibile predirre quale e’ il secondo segmento di informazione che produrra’ l’hash richiesto, devi iterare casualmente attraverso tutti i dati possibili finche’ non trovi quel segmento di dati che produce l’hash di cui hai bisogno. 

## Tecnologie di Ethereum

**La Ethereum Virtual Machine**

La Ethereum Virtual Machine e’ l’innovazione principale del progetto Ethereum. Si tratta di una virtual machine progettata per essere eseguita da tutti i partecipanti in una rete peer-to-peer, puo’ leggere e scrivere su una blockchain sia codice eseguibile che dati, verificare firme digitali, e puo’ far girare codice in maniera quasi Turing-completa. Eseguira’ del codice solo quando riceve un messaggio verificato da una firma digitale, e quando l’infomazione che e’ contenuta nella blockchain le dira’ che e’ appropriato farlo. 

**La Rete di Consenso Decentralizzato e la Blockchain Generalizzata**

Ethereum e’ una rete peer to peer dove ogni peer salva la stessa copia del database blockchain e fa girare una Ethereum virtual machine per mantenere ed alterare il suo stato. La Proof of work e’ integrata nella tecnologia blockchain facendo si’ che la creazione di un nuovo blocco richieda che tutti i membri del network realizzino la proof-of-work. Il consenso e’ ottenuto tramite l’incentivizzazione per i peer ad accettare sempre la sequenza piu’ lunga di blocchi nella blockchain mediante la distribuzione di un gettone di valore crittografico: l’ether.
 
In questo modo abbiamo una nuova tecnologia che sicuramente non rientra nel modello client-server ma non si qualifica neppure all’interno di un tradizionale modello peer-to-peer in quanto la sua esistenza e’ incentivizzata, ovvero si puo’ ragionevolmente stare certi che provvedera’ un servizio deterministico consistente. 

Data la sua natura allargata e la sicurezza criptografica costruita al suo interno puo’ fungere da third party, capace di arbitrare senza fiducia e senza interferenza di parti esterne. Attraverso l’uso di decisioni tipiche del sistema di cryptocurrencies fatte dal software possiamo avere conseguenze finanziarie per le persone, le organizzazioni o persino per altro software. 

Cio’ da’ agli sviluppatori un nuovo modo per rendere possibili le interazioni tra le parti su internet. 

Mentre spiegheremo i vari temi dello sviluppo delle decentralized apps illustreremo il motivo del loro utilizzo e faremo del nostro meglio per spiegare l’importanza di ciascuna. 

### Ulteriori letture

* [Libro Bianco di Ethereum - tradotto in Italiano ](https://github.com/ethereum/wiki/wiki/%5BItalian%5D-Libro-Bianco)
* [Yellow Paper di Ethereum, la specificazione formale - inglese](https://github.com/ethereum/yellowpaper)
* [Blog di Ethereum - inglese](https://blog.ethereum.org/)

_traduzione di M.Terzi - @terzim -  [LinkedIn](https://uk.linkedin.com/in/massimilianoterzi)_
