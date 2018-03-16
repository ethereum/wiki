---
name: Impostare il proprio ambiente di sviluppo Ethereum
category: 
---

Lo sviluppo su Ethereum e’ stato concepito per essere estremamente facile da imparare per gli sviluppatori – e i linguaggi di programmazione sono abbastanza simili allo Javascript che chiunque conosca quel linguaggio dovrebbe acquisirne padronanza piuttosto velocemente. 

Ci sono tre programmi che ogni sviluppatore dovrebbe scaricare – Alethzero, Mist e Mix. 

* **Alethzero** e’ un client a interfaccia grafica con caratteristiche avanzate come chain private, force mining e una webkit suite completa. 

* **Mist** e’ il browser delle Dapp e il client per il mining nel quale gli utenti accederanno alle vostre Dapps. 

* Infine, **Mix** e’ un ambiente di sviluppo integrato – concepito appositamente per sviluppare e debuggare i contratti e i loro corrispettivi front-end. 

## Requisiti software:

Impostare un ambiente di sviluppo dovrebbe essere abbastanza semplice per chiunque ha mai progettato una pagina web prima d’ora – abbiamo tre programmi specifici che dovreste scaricare: 

In primo luogo, [scaricate](https://github.com/ethereum/cpp-ethereum/wiki) l’ultima versione stabile di Alethzero, il nostro client scritto in C++, e installatelo sul vostro sistema operativo. Se avete problemi con l’ultima versione stabile di sviluppo, provate a passare alla versione cutting edge, che dovrebbe risolvere alcuni dei problemi. Se invece scegliete di fare un build della vostra versione, allora le istruzioni sono qui.

In secondo luogo, installate [Mix](https://github.com/ethereum/cpp-ethereum/releases) il nostro ambiente di sviluppo integrato disponibile per Windows e Mac. Se utilizzate Linux, seguite le istruzioni qui per installare Mix.

Infine, installate [Mist](https://github.com/ethereum/go-ethereum#automated-dev-builds) per testare le vostre Dapps e calibrare i vostri front-end a mano a mano che li sviluppate.

## Extras

Un text editor o Mix puo’ essere utilizzato per creare il codice backend dei contratti che scriveremo. Per il linguaggio Serpent vi suggeriamo di programmare il vostro editor affinche’ consideri i contratti scritti in Serpent e salvati con il suffisso ‘.se’ come sintassi python e per il linguaggio Solidity vi consigliamo di salvare i files con il suffisso ‘.sol’.

Il live refresh non e’ raccomandato mentre lavorate sulla vostra front-end in html, dato che non e’ stato testato appieno. 

## Installare Alethzero

Il nostro ambiente di sviluppo integrato Mix e’ al momento in constante sviluppo e nonostante alcune caratteristiche siano gia’ presenti in questo tutorial ci concentreremo principalmente sullo sviluppo dei contratti, e la costruzione dei frontend utilizzando il nostro client di sviluppo Alethzero. Alethzero ha un compilatore gia’ integrato, una console javascript, e gli strumenti necessari per dare un’occhiata allo stato della blockchain.

A meno che non sia espressamente specificato, questo tutorial deve essere eseguito su Alethzero utilizzando una chain privata e senza connettersi alla rete – inserire contratti nella rete di test dovrebbe essere riservato a quei contratti che volete condividere con altri. Quando caricate alethzero in questa modalita’ e’ possibile che altri accedano alla vostra chain fintanto che utilizzano lo stesso nome e usano la funzione ‘connect-to-peer’ per connettersi direttamente.
