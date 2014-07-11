### Una Piattaforma di Nuova Generazione per i Contratti Intelligenti e le Applicazioni Decentralizzate

Quando Satoshi Nakamoto attivò per primo, nel Gennaio 2009, la blockchain del Bitcoin, stava introducendo due concetti radicali e altamente non testati fino ad allora. Il primo è il "bitcoin", una moneta online peer-to-peer decentralizzata che mantiene un valore senza nessun supporto, [valore intrinseco](http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/) o emittente centrale. Finora, il "Bitcoin" come unità monetaria ha catturato la maggior parte dell'attenzione del pubblico, sia in termini di aspetti politici di una moneta senza una banca centrale che per l' estrema volatilità del prezzo verso l'alto e verso il basso. Comunque c'è anche un'altra, ugualmente importante, parte del grandioso esperimento di Satoshi: il concetto di una blockchain basata sul  proof-of-work che permette un consenso pubblico sul sistema delle transazioni. Il Bitcoin come un'applicazione può essere descritto come un sistema first-to-file: se un'entità ha 50 BTC, e simultaneamente invia gli stessi 50 BTC ad A e a B, solamente la transazione che viene confermata per prima sarà processata. Non c'è nessun intrinseco modo di determinare quale delle due transazioni è avvenuta prima, e per decenni questo ha ostacolato lo sviluppo delle monete digitali decentralizzate. La blockchain di Satoshi era la prima soluzione decentralizzata. Ed ora, l'attenzione sta iniziando rapidamente a spostarsi verso questa seconda parte della tecnologia Bitcoin, e come  questa seconda parte della tecnologia di Bitcoin, e come il concetto di blockchain possa essere utilizzato per qualcosa di più che il denaro.

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