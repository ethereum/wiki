### Un contrato inteligente de próxima generación y una plataforma de aplicación descentralizada

El desarrollo de Bitcoin de Satoshi Nakamoto en el año 2008[<sup>[1a]</sup>](http://nakamotoinstitute.org/bitcoin/)[<sup>[1b]</sup>](http://www.newyorker.com/magazine/2011/10/10/the-crypto-currency)–2009[<sup>[1c]</sup>](https://en.bitcoin.it/wiki/Category:History)[<sup>[1d]</sup>](https://blockexplorer.com/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f) ha sido aclamado como una evolución radical en el dinero y la moneda,siendo el primer ejemplo de un activo digital que simultáneamente no tiene respaldo o valor intrínseco[<sup>[2]</sup>](https://bitcoinmagazine.com/articles/you-say-bitcoin-has-no-intrinsic-value-twenty-two-reasons-to-think-again-1399454061/) ni un emisor o controlador centralizado. Sin embargo, otra parte del experimento Bitcoin, discutiblemente más importante, es la subyacente tecnología blockchain como una herramienta de consenso distribuído. La atención está rápidamente comenzando a trasladarse a este otro aspecto de Bitcoin. Las aplicaciones alternativas comúmente citadas de la teconología blockchain incluyen el uso de activos digitales en blockchain para representar monedas personalizadas e intrumentos financieros(colored coins),[<sup>[3]</sup>](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit) la posesión de un dispositivo físico subyacente (propiedad inteligente),[<sup>[4]</sup>](https://en.bitcoin.it/wiki/Smart_Property) activos no fungibles tales como nombres de dominio (Namecoin)[<sup>[5]</sup>](http://namecoin.org) , así como aplicaciones más complejas que implican tener activos digitales controlados directamente por un código que implementa reglas arbitrarias conocidas como contratos inteligentes[<sup>[6]</sup>](https://en.bitcoin.it/wiki/Contracts) o incluso organizaciones autónomas descentralizadas basadas en blockchain (DAOs por sus siglas en inglés)[<sup>[7]</sup>](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/)

Lo que Ethereum intenta es proveer un blockchain con un lenguaje de programación integrado Turning completo, completamente desarrollado que puede ser utilizado para crear "contratos" que pueden ser utilizados para codificar funciones de transición de estado arbitrarias, permitiendo a los usuarios crear cualquiera de los sistemas arriba descritos, así como muchos otros que aún no hemos imaginados, simplemente escribiendo la lógica en pocas líneas de código.

### Tabla de contenidos

* [Introducción a Bitcoin y Conceptos existentes] (#introduccion-a-bitcoin-y-conceptos-existentes)
    * [Historia](#historia)
    * [Bitcoin como un sistema de transición de estado](#bitcoin-como-un-sistema-de-transicion-de-estado)
    * [Minería](#minería)
    * [Merkle Trees](#merkle-trees)
    * [Árboles Merkle](#arboles-merkle)
    * [Aplicaciones Alternativas de Blockchain](#aplicaciones-alternativas-de-blockchain)
    * [Alternative Blockchain Applications](#alternative-blockchain-applications)
    * [Escritura](#escritura)
    * [Scripting](#scripting)

* [Ethereum](#ethereum)
* [Ethereum](#ethereum)
    * [Cuentas Ethereum](#cuentas-ethereum)
    * [Ethereum Accounts](#ethereum-accounts)
    * [Mensajes y transacciones](#mensajes-y-transacciones)
    * [Messages and Transactions](#messages-and-transactions)
    * [Función de transición del estado de Ethereum](#funcion-de-transicion-de-estado-de-ethereum)
    * [Ethereum State Transition Function](#ethereum-state-transition-function)
    * [Ejecución de código](#code-execution)
    * [Code Execution](#code-execution)
    * [Cadena de bloques y minería](#cadena-de-bloques-y-mineria)
    * [Blockchain and Mining](#blockchain-and-mining)
* [Aplicaciones](#aplicaciones)
* [Applications](#applications)
    * [Sistemas de Moneda](#sistemas-de-monedas)
    * [Token Systems](#token-systems)
    * [Derivados financieros](#derivados-financieros)
    * [Financial derivatives](#financial-derivatives-and-stable-value-currencies)
    * [Sistemas de identidad y reputación](#sistemas-de-identidad-y-reputacion)
    * [Identity and Reputation Systems](#identity-and-reputation-systems)
    * [Almacenamiento de archivos descentralizado](#almacenamiento-de-archivos-descentralizado)
    * [Decentralized File Storage](#decentralized-file-storage)
    * [Organizaciones autónomas descentralizadas](#organizaciones-autonomas-descentralizadas)
    * [Decentralized Autonomous Organizations](#decentralized-autonomous-organizations)
    * [Aplicaciones adicionales](#aplicaciones-adicionales)
    * [Further Applications](#further-applications)
* [Miscelánea e inquietudes](#miscelanea-e-inquietudes)
* [Miscellanea And Concerns](#miscellanea-and-concerns)
    * [Implementación modificada de GHOST](#implementacion-modificada-de-ghost)
    * [Modified GHOST Implementation](#modified-ghost-implementation)
    * [Fees](#fees)
    * [Cuotas](#cuotas)
    * [Computation And Turing-Completeness](#computation-and-turing-completeness)
    * [Computación y Completitúd Turning](#computación y completitud-turning)
    * [Moneda y emisión](#moneda-y-emision)
    * [Currency And Issuance](#currency-and-issuance)
    * [Centralización de minería](#centralizacion-de-mineria)
    * [Mining Centralization](#mining-centralization)
    * [Escalabilidad](#escalabilidad)
    * [Scalability](#scalability)
+ [Conclusión](#conclusion)
* [Conclusion](#conclusion)
* [Notas, referencias y lecturas adicionales](#notas-referencias-y-lecturas-adicionales)
* [Notes, References and Further Reading](#notes-references-and-further-reading)

## Introduccuón a Bitcoin y conceptos existentes
## Introduction to Bitcoin and Existing Concepts

### Historia
### History

El concepto de moneda digital descentralizada, así como aplicaciones alternativas como registros propietarios, han estado alrededor por décadas. El anonimato en protocolos de transacciones electrónicas de los años 80 y de los años 90 fueron en su mayoría dependientes de criptografía primitiva conocida como Chaumian Blinding

The concept of decentralized digital currency, as well as alternative applications like property registries, has been around for decades. The anonymous e-cash protocols of the 1980s and the 1990s were mostly reliant on a cryptographic primitive known as Chaumian Blinding.[<sup>[8]</sup>]

Chaumian Blinding proporcionó estas nuevas monedas con altos grados de privacidad, pero sus protocolos subyacentes en gran medida no lograron ganar tracción debido a su dependencia de un intermediario centralizado. En 1988, el b-money de Wei Dai 

se convirtió en la primer propuesta para intriducir la idea de crear dinero a través de la solución de rompecabezas computacionales así como del consenso descentralizado, pero la prouesta era escasa en los detalles sobre cómo podría ser implementado el consenso descentralizado. En 2005, Hal Finney introduce un concepto de prueba de concepto reusable, 

un sistema que usa ideas de b-money junto con rompecabezas de dificultad computacional de Adam Back para crear el concepto de cryptomoneda, pero una vez más no estuvo a la altura del ideal dependiendo de cómputo de confianza como backend. En 2009, una moneda descentralizada fué por primera vez implementada en la práctica por Satoshi Nakamoto,


(https://en.wikipedia.org/wiki/Blind_signature) Chaumian Blinding provided these new currencies with high degrees of privacy, but their underlying protocols largely failed to gain traction because of their reliance on a centralized intermediary. In 1998, Wei Dai's b-money[<sup>[9]</sup>]

(http://www.weidai.com/bmoney.txt) became the first proposal to introduce the idea of creating money through solving computational puzzles as well as decentralized consensus, but the proposal was scant on details as to how decentralized consensus could actually be implemented. In 2005, Hal Finney introduced a 

concept of reusable proofs of work,[<sup>[10]</sup>](http://www.finney.org/~hal/rpow/) a system which uses ideas from b-money together with Adam Back's computationally difficult Hashcash[<sup>[11]</sup>](http://www.hashcash.org/) puzzles to create a concept for a cryptocurrency, but once again fell short of the ideal by relying on trusted computing as a backend. In 2009, a decentralized currency was for the first time implemented in practice by Satoshi Nakamoto,[<sup>[1c]</sup>](https://en.bitcoin.it/wiki/Category:History)[<sup>[1d]</sup>]

combinando primitivas establecidas para el manejo de posesión a través de una llave criptográfica pública con un algoritmo de consenso para mantener el registro de quien posee monedas, conocido como "prueba de trabajo".

(https://blockexplorer.com/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f) combining established primitives for managing ownership through public key cryptography with a consensus algorithm for keeping track of who owns coins, known as "proof of work."

El mecanismo detrás de la prueba de trabajo fue un gran avance puesto que simultáneamenta resolvía dos problemas. Primero, este provee un algoritmo de conseso simple y moderadamente efectivo, permitiendo nodos en la red para acordar colectivamente un conjunto de actualizaciones de estado del libro mayor de Bitcoin. En segundo lugar, proporcionó un mecanismo para permitir la entrada linre al proceso de consenso, resolviendo el problema político de decidir quién influye en el consenso, a tiempo que previene los ataques de Sybil. 

The mechanism behind proof of work was a breakthrough because it simultaneously solved two problems. First, it provided a simple and moderately effective consensus algorithm, allowing nodes in the network to collectively agree on a set of updates to the state of the Bitcoin ledger. Second, it provided a mechanism for allowing free entry into the consensus process, solving the political problem of deciding who gets to influence the consensus, while simultaneously preventing Sybil attacks.[<sup>[12]</sup>](https://en.wikipedia.org/wiki/Sybil_attack)


Logra esto sustituyendo una barrera formal a la participación, como el requisito de estar registrado como una entidad única en una lista particular, con una barrera económica: el peso de un sólo nodo en el proceso de votación por consenso es directamente proporcional a la potencia de cálculo que aporta el nodo. Desde entonces, se ha propuesto un enfoque alternativo denominado prueba de trabajo (_proof of _stake), que calcula el peso de un nodeo como proporcional a sus tendencias de divisas y no a sus recursos computacionales. La discusión sobre los méritos relativos de los dos enfoques está más allá del alcance de este documento, pero debe tenerse en cuenta que ambos enfoques se pueden utilizar para servir como columna vertebral de una criptomoneda.

It does this by substituting a formal barrier to participation, such as the requirement to be registered as a unique entity on a particular list, with an economic barrier - the weight of a single node in the consensus voting process is directly proportional to the computing power that the node brings. Since then, an alternative approach has been proposed called _proof of stake_, calculating the weight of a node as being proportional to its currency holdings and not its computational resources. The discussion concerning the relative merits of the two approaches is beyond the scope of this paper but it should be noted that both approaches can be used to serve as the backbone of a cryptocurrency.

### Bitcoin como un sistema de transición de estado
### Bitcoin As A State Transition System

![statetransition.png](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/statetransition.png)

Desde una punto de vista técnico, el libro mayor de una critomoneda como el Bitcoin puede ser considerado como un sistema de transición de estado, donde hay un "estado" que consiste en el estaado de propiedad de todos los bitcoins existentes y una "función de transición de estado" que toma un estado y una transacción y 
produce como salida un nuevo estado que es el resultado. 

From a technical standpoint, the ledger of a cryptocurrency such as Bitcoin can be thought of as a state transition system, where there is a "state" consisting of the ownership status of all existing bitcoins and a "state transition function" that takes a state and a transaction and outputs a new state which is the result. 

En un sistema bancario estándar, por ejemplo, el estado es un balance, una transacción es una solicitúd para mover $X de A a B, y la función de transición de estado reduce el valor de la cuenta de A en $X y aumenta el valor de B cuenta por $X. Si la cuenta de A tiene menos de $ X en primer lugar, la función de transición de estado devuelve un error. Por lo tanto, se puede definir formalmente:

    APPLY(S,TX) -> S' or Error

In a standard banking system, for example, the state is a balance sheet, a transaction is a request to move $X from A to B, and the state transition function reduces the value of A's account by $X and increases the value of B's account by $X. If A's account has less than $X in the first place, the state transition function returns an error. Hence, one can formally define:

    APLICAR(S,TX) -> S' || ERROR

En el sistema bancario definido arriba:
    APLICAR({ Alice: $50, Bob: $50 }, "envía $20 de Alice a Bob") = { Alice: $30, Bob: $70 }

In the banking system defined above:

    APPLY({ Alice: $50, Bob: $50 },"send $20 from Alice to Bob") = { Alice: $30, Bob: $70 }

Pero:
    APLICAR({ Alicia: $50, Bob: $50}, "envía %70 de Alice a Bob") = ERROR

But:
    APPLY({ Alice: $50, Bob: $50 },"send $70 from Alice to Bob") = ERROR

El "estado" en Bitcoin es la colección de todas las monedas (técnicamente, resultados de transación no gastados" o UTXO) que han sido minados y aún no gastados, con cada UTXO teniendo una denominación y un dueño (definido por una dirección de 20 bytes que es esencialmente una llave criptográfica pública)

The "state" in Bitcoin is the collection of all coins (technically, "unspent transaction outputs" or UTXO) that have been mined and not yet spent, with each UTXO having a denomination and an owner (defined by a 20-byte address which is essentially a cryptographic public key<sup>[Note 1]</sup>). 

Una transacción contiene una o más entradas, cada una haciendo referencia a un UTXO existente y a una firma criptográdica producida por la llave privada asociada a la dirección del dueño, y una o más salidas, cada una conteniendo un nuevo UTXO para para agregar al estado.

A transaction contains one or more inputs, with each input containing a reference to an existing UTXO and a cryptographic signature produced by the private key associated with the owner's address, and one or more outputs, with each output containing a new UTXO for addition to the state.

La función de transición `APLICAR(S,TX) -> S'` puede ser definida aproximadamente de la siguiente manera:
The state transition function `APPLY(S,TX) -> S'` can be defined roughly as follows:

1. Para cada entrada en TX:
    * Si el UTXO referenciado no está en `S`, devuelve un error.
    * Si la firma proporcionada no coincide con el dueño del UTXO, devuelve un error.
2. Si la suma de las denominaciones de todas las entradas UTXO es menor que la suma de las denominaciones de todas las salidas UTXO, devuelve un error.
3. Devuelve `S'` con todas las entradas UTXO eliminadas y todas las salidas UTXO agregadas.

1. For each input in `TX`:
    * If the referenced UTXO is not in `S`, return an error.
    * If the provided signature does not match the owner of the UTXO, return an error.
2. If the sum of the denominations of all input UTXO is less than the sum of the denominations of all output UTXO, return an error.
3. Return `S'` with all input UTXO removed and all output UTXO added.

La primera mitad de el primer paso previene que el emisor de la transacción envíe monedas que no existen, la segunda mitad del primer paso previene que el emisor gaste monedas de otras personas, el segundo paso impone la conservación del valor. Para utilizar esto para pago, el protocolo es el siguiente. Supongamos que Alice quiere enviar 11.7 BTC a Bob. En primer lugar, Alice buscará un conjunto de UTXO disponible que posea y que totalizará al menos 11.7 BTC. Siendo realistas, Alice no podrá obtener exactamente 11.7 BTC; digamos que lo menos que puede obtener es 6 + 4 + 2 = 12. Luego crea una transacción con esas tres entradas y dos salidas. La primera salida será 11.7 BTC con la dirección de BOB como su propietario, y la segunda salida será el restante 0.3 BTC "cambio". Si Alice no reclama este cambio enviándolo a una dirección de su propiedad, el minero podrá reclamarlo.

The first half of the first step prevents transaction senders from spending coins that do not exist, the second half of the first step prevents transaction senders from spending other people's coins, and the second step enforces conservation of value. In order to use this for payment, the protocol is as follows. Suppose Alice wants to send 11.7 BTC to Bob. First, Alice will look for a set of available UTXO that she owns that totals up to at least 11.7 BTC. Realistically, Alice will not be able to get exactly 11.7 BTC; say that the smallest she can get is 6+4+2=12. She then creates a transaction with those three inputs and two outputs. The first output will be 11.7 BTC with Bob's address as its owner, and the second output will be the remaining 0.3 BTC "change". If Alice does not claim 
this change by sending it to an address owned by herself, the miner will be able to claim it.

### Minería
### Mining

![block_picture.jpg](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/block.png)

Si tenemos acceso a un servicio centralizado confiable, este sistema sería trivial de implementar; podría ser codificado exactamente como se describió, utilizando un disco duro de un servidor centralizado para mantener el registro del estado. Sin embargo, con Bitcoin estamos tratando de construir un sistema de moneda descentralizado, por ello necesitamos combinar el sistema de transición de estado con un sistema de consenso para garantizar que todos estén de acuerdo en el orden de las transacciones. El proceso de consenso descentralizado de Bitcoin requiere los nodos en la red intenten continuamente producir paquetes de transacciones llamados "bloques". La red está destinada a crear un bloque aproximadamente cada diez minutos, cada bloque conteniendo una estampa de tiempo, un nonce(una palabra inventada para la ocasión), una referencia a (es decir, hash) del bloque anterior y una lista de todas las transacciones que han tenido lugar desde el anteriror bloque. Con el tiempo esto crea una "Cadena de bloques" persistente y en constante crecimiento que se actualiza contínuamente para representar el estado más reciente del libro mayor de Bitcoin.

If we had access to a trustworthy centralized service, this system would be trivial to implement; it could be coded exactly as described, using a centralized server's hard drive to keep track of the state. However, with Bitcoin we are trying to build a decentralized currency system, so we will need to combine the state transition system with a consensus system in order to ensure that everyone agrees on the order of transactions. Bitcoin's decentralized consensus process requires nodes in the network to continuously attempt to produce packages of transactions called "blocks". The network is intended to create one block approximately every ten minutes, with each block containing a timestamp, a nonce, a reference to (i.e., hash of) the previous block and a list of all of the transactions that have taken place since the previous block. Over time, this creates a persistent, ever-growing, "blockchain" that continually updates to represent the latest state of the Bitcoin ledger.

Los algoritmos para comprobar si un bloque es válido, expresado en este paradigma como sigue:
The algorithm for checking if a block is valid, expressed in this paradigm, is as follows:

1. Verifica si el bloque previo referenciado por el bloque existe y es válido.
1. Check if the previous block referenced by the block exists and is valid.
2. Verifica que la estampa de tiempo del bloque es mayor que la del bloque previo
y menor que 2 horas en el futuro.
2. Check that the timestamp of the block is greater than that of the previous block<sup>[Note 2]</sup> and less than 2 hours into the future
3. Verifica que la prueba de trabajo en el bloque sea válida.
3. Check that the proof of work on the block is valid.
4. Sea `S[0]` el estado al final del bloque previo.
4. Let `S[0]` be the state at the end of the previous block.
5. Suponiendo `TX` como la lista de transacciones del bloque con `n` transacciones. Para todo `i` en `0...n-1`, implica que `S[i+1] = APLICA(S[i+1], TX[i])` Si alguna aplicación devuelve un error, sale y devuelve falso.

5. Suppose `TX` is the block's transaction list with `n` transactions. For all `i` in `0...n-1`, set `S[i+1] = APPLY(S[i],TX[i])` If any application returns an error, exit and return false.
6. Devuelve true, y registra `S[n]` como el estado al final de este bloque
6. Return true, and register `S[n]` as the state at the end of this block.

Esencialmente, cada transacción en el bloque deverá proveer un estádo de transición válido del que era es estado canónico antes de que la transcacción fuera ejecutada a algún nuevo estado. Tenga en cuenta que el estado no está codificado en el bloque de ninguna manera; es puramente una abstracción que debe ser recordada por el nodo de validación y sólo puede (de una forma segura) computarse para cualquier bloque comenzando desde el estado de génesis y aplicando secuencualmente cada transacción en el bloque. Además, tenga en cuenta que el orden en el cual el minero incluye las transacciones en el bloque importa; si hay dos transacciones A y B en un bloque de modeo que B gasta un UTXO creado por A, entonces el bloque será válido si A entra antes que B, pero no de otra manera.

Essentially, each transaction in the block must provide a valid state transition from what was the canonical state before the transaction was executed to some new state. Note that the state is not encoded in the block in any way; it is purely an abstraction to be remembered by the validating node and can only be (securely) computed for any block by starting from the genesis state and sequentially applying every transaction in every block. Additionally, note that the order in which the miner includes transactions into the block matters; if there are two transactions A and B in a block such that B spends a UTXO created by A, then the block will be valid if A comes before B but not otherwise.

La condición de validez presente en la lista anterior que no se encuentra en otros sistemas es el requisito de "prueba de trabajo". La condición precisa es que el hash doble SHA254 
de cada bloque, tratado como un cúmero de 256 bits, debe ser menot que un objectivo dinámicamente ajustado, que a partir del momento de escribir esto es aproximadamente 2

The one validity condition present in the above list that is not found in other systems is the requirement for "proof of work". The precise condition is that the double-SHA256 hash[<sup>[13]</sup>](https://en.wikipedia.org/wiki/SHA-2) of every block, treated as a 256-bit number, must be less than a dynamically adjusted target, which as of the time of this writing is approximately 2<sup>187</sup>. 


El propósito de esto es hacer la creación de los bloques computacionalmente "difícil", evitando así que los atacantes Sybil

rehagan toda la cadena de bloques, a su favor. Debido a que SHA256 está diseñado para ser una función pseudoaleatoria completamente inpredecible, el único camino para crear un bloque válido es simplemente prueba y error, repetidamente incrementando el nonce y y viendo si el hash nuevo coincide.

The purpose of this is to make block creation computationally "hard", thereby preventing Sybil attackers[<sup>[12]</sup>](https://en.wikipedia.org/wiki/Sybil_attack) from remaking the entire blockchain in their favor. Because SHA256 is designed to be a completely unpredictable pseudorandom function, the only way to create a valid block is simply trial and error, repeatedly incrementing the nonce and seeing if the new hash matches.

Al momento de escribir esto, para un objectivo de ~2

la red debe realizar un promedio de ~ 2
intentos antes de encontrar un bloque válido; en general, el objetivo es recalibrado por la red cada 2016 bloques, de modo que en promedio, un nodeo en la red produce un nuevo bloque cada diez minutos. Con la finalidad de compensar a los mineros por este trabajo computacional, el minero de cada bloque tiene derecho a incluir una transacción que se 12.5 BTC de la nada. Además, si cualquier transacción tiene una denominación total más allá en sus entradas que en sus productos, la diferencia también le corresponde al minero como una "tarifa de transacción". Por cierto, este es también el único mecanismo por el cual se emiten BTC; el estado de génesis no contenía ninguna moneda en absoluto.
.

At the time of writing, for a target of ~2<sup>187</sup>, the network must make an average of ~2<sup>69</sup> tries before a valid block is found; in general, the target is recalibrated by the network every 2016 blocks so that on average a new block is produced by some node in the network every ten minutes. In order to compensate miners for this computational work, the miner of every block is entitled to include a transaction giving themselves 12.5 BTC out of nowhere. Additionally, if any transaction has a higher total denomination in its inputs than in its outputs, the difference also goes to the miner as a "transaction fee". Incidentally, this is also the only mechanism by which BTC are issued; the genesis state contained no coins at all.

En favor de un mejor entendimiendo del propósito de minería, examinemos qué sucede en el evento de un atacante malicioso. Debido a que la subyacente criptografía del Bitcoin es segura, el objetivo del atacante será la única parte del sistema Bitcoin que no está protegida criptográficamente de forma directa: el orden de las transacciones. La estrategia del atacante es simple:

1. Enviar 100 BTC a un comerciante a cambio de un producto (preferiblemente un bien digital de entrega rápida)
2. Espera a que el producto sea entregado
3. Produce otra transacción enviando la misma cantidad de 100 BTC a si mismo.
4. Trata de convencer a la red que la transacción a él mismo ocurrió primero.

In order to better understand the purpose of mining, let us examine what happens in the event of a malicious attacker. Since Bitcoin's underlying cryptography is known to be secure, the attacker will target the one part of the Bitcoin system that is not protected by cryptography directly: the order of transactions. The attacker's strategy is simple:

1. Send 100 BTC to a merchant in exchange for some product (preferably a rapid-delivery digital good).
2. Wait for the delivery of the product.
3. Produce another transaction sending the same 100 BTC to himself.
4. Try to convince the network that his transaction to himself was the one that came first.

Una vez que el paso (1) sucede, después de algunos minutos algún minero  incluirá la transacción en el bloque, digamos el bloque número 270,000. Después de una hora, 5 o más bloques han sido añadidos a la cadena después de ese bloque, con cada uno de esos bloque indirectamente apuntando a la transacción, así "confirmando" la transacción.

En este punto el atacante crea otra transacción enviando 100 BTC a sí mismo. Si el atacante simplemente libera al aire la transacción, esta no sería procesada;
los minero tratarían de correr `APLICAR(S,TX)` y se percatarían que `TX` utiliza un UTXO que ya no es parte del estado. En vez de esto, el atacante crea un "fork" de la cadena del bloques Bitcoin, comenzando por minar otra versión del bloque 270,000 apuntando al mismo bloque 269,999 como padre pero con la nueva transacción en lugar de la anterior. Puesto que la información del bloque es diferete esto require rehacer la prueba de trabajo del bloque de interés. 

Once step (1) has taken place, after a few minutes some miner will include the transaction in a block, say block number 270,000. After about one hour, five more blocks will have been added to the chain after that block, with each of those blocks indirectly pointing to the transaction and thus "confirming" it. At this point, the merchant will accept the payment as finalized and deliver the product; since we are assuming this is a digital good, delivery is instant. Now, the attacker creates another transaction sending the 100 BTC to himself. If the attacker simply releases it into the wild, the transaction will not be processed; miners will attempt to run `APPLY(S,TX)` and notice that `TX` consumes a UTXO which is no longer in the state. So instead, the attacker creates a "fork" of the bitcoin blockchain, starting by mining another version of block 270,000 pointing to the same block 269,999 as a parent but with the new transaction in place of the old one. Because the block data is different, this requires redoing the proof of work for the concerned block. 

Además, la nueva versión del bloque 270,000 del atacante tiene un hash diferente, así que los bloques originales 270,001 al 270,005 no 'apuntan' a este; Así, la cadena original y la nueva cadena del atacante están completamente separadas. La regla es que el bloque con la cadena más larga es el verdadero, y así los moneros legítimos trabajarán en la cadena 270005 mientras que el atacante sólo está trabajando en la cadena 270,000. Para que el atacante pueda hacer su cadena la más larga, necesitaría tener máyor poder computacional que el resto de la red combinada para poder alcanzarla (por lo tanto, "51% ataque"). Los bloques bitcoin dependen del hash de todos los bloques anteriores. 

Furthermore, the attacker's new version of block 270,000 has a different hash, so the original blocks 270,001 to 270,005 do not "point" to it; thus, the original chain and the attacker's new chain are completely separate. The rule is that in a fork the longest blockchain is taken to be the truth, and so legitimate miners will work on the 270005 chain while the attacker alone is working on the 270,000 chain. In order for the attacker to make his blockchain the longest, he would need to have more computational power than the rest of the network combined in order to catch up (hence, "51% attack"). Bitcoin blocks depend on the hash of all previous blocks. 

Un atacante con inmenso poder computacional puede rehacer la prueba de trabajo (PoW) por una considerada suma de bloques y eventualmente ganar muchos bitcoins, pero como es descrito en la publicación de Satoshi,

An attacker with immense computing power can redo the proof of work (PoW) for a considerate amount of blocks and can eventually gain a lot of bitcoins but as described in Satoshi's paper,[<sup>[1a]</sup>](http://nakamotoinstitute.org/bidtcoin/) 

la recompensa de minar un bloque válido es mucho más mayor a la de quebrantar la red. Pero a la luz de la caida en recompensas de minería, esto último puede no ser verdad.

the reward to mine a valid block is much more than to disrupt the network. But in light of falling mining rewards the same does not hold true. 

### Merkle Trees
### Árboles Merkle
![SPV1 in bitcoin](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/spv1.png)

_01: it suffices to present only a small number of nodes in a Merkle tree to give a proof of the validity of a branch._
_01: basta con presentar solo un pequeño número de nodeos en un árbol de Merkle para dar una prueba de la validez de la rama._

![SPV2 in bitcoin](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/spv2.png)

_02: any attempt to change any part of the Merkle tree will eventually lead to an inconsistency somewhere up the chain._
_02: cualquier intento de cambiar cualquier parte del arbol Merkle eventualmente llevará a inconsistencias en algún lugar de la cadena._

Una importante característica de escalabilidad de Bitcoin es que el bloque es guardado en una estructura de datos de varios niveles. El "hash" de un bloque es en realidad solo el hash del encabezado del bloque, un fragmento de datos de aproximadamente 200 bytes que contiene la marca de tiempo, nonce, hash del bloque anterior y el hash raíz de una estructura de datos llamada Árbol Merkle que almacena todas las transacciones en el bloque. Un árbol Merkle es un tipo de árbol binario, compuesto por un conjunto de nodos hoja en la parte inferior del árbol que contiene los datos subyacentes, un conjunto de nodos intermedios donde cada nodo es el hash de sus dos hijos, y finalmente un único nodo raíz, también formado a partir del hash de sus dos hijos, que representa la "parte superior" del árbol. El propósito del árbol de Merkle es permitir que los datos en un bloque se entreguen por partes: un nodo puede descargar sólo el encabezado de un bloque desde una fuente, la pequeña parte del árbol relevante para ellos desde otra fuente, y aún estar seguro que todos los datos son correctos. La razón por la cual esto funciona es que los hash se propagan hacia arriba: si un usuario malicioso ontenta intercambiar una transacción falsa en la parte inferior de un árbol de Merkle, este cambio causará un cambio en el nodo anterior y luego un cambio en el nodo anterior, finalmente cambiando la raíz del árbol y por lo tanto, el hash del bloque, haciendo que el protocolo lo registre como un bloque completamente diferente (casi con certeza con una prueba inválida de trabajo).

An important scalability feature of Bitcoin is that the block is stored in a multi-level data structure. The "hash" of a block is actually only the hash of the block header, a roughly 200-byte piece of data that contains the timestamp, nonce, previous block hash and the root hash of a data structure called the Merkle tree storing all transactions in the block. A Merkle tree is a type of binary tree, composed of a set of nodes with a large number of leaf nodes at the bottom of the tree containing the underlying data, a set of intermediate nodes where each node is the hash of its two children, and finally a single root node, also formed from the hash of its two children, representing the "top" of the tree. The purpose of the Merkle tree is to allow the data in a block to be delivered piecemeal: a node can download only the header of a block from one source, the small part of the tree relevant to them from another source, and still be assured that all of the data is correct. The reason why this works is that hashes propagate upward: if a malicious user attempts to swap in a fake transaction into the bottom of a Merkle tree, this change will cause a change in the node above, and then a change in the node above that, finally changing the root of the tree and therefore the hash of the block, causing the protocol to register it as a completely different block (almost certainly with an invalid proof of work).

El protocolo árbol Merkle es discutiblemente escencial para la sostenibilidad en el largo plazo. Un "nodo completo" en la red Bitcoun, uno que guarda y procesa la todalidad de cada bloque, ocupa cerca de 15GB de espacio en la red Bitcoin a partir de abril de 2015, y está creciendo más de un gigabyte por mes- Actualmente, esto es viable para algunas computadoras de escritorio y no para teléfonos, y más adelante en el futuro solo las empresas y los aficionados podrán participar. Un protocolo conocido como "verificacion de pago simplificada" (SPV) permite que exita otra clase de nodos, llamados "nodos luminosos", que descargan los encabezados de los bloques, verifican la prueba de trabajo en los encabezados del bloque y luego solo descargan las "ramas". "Asociado con transacciones que son relevantes para ellos- Esto permite que los nodos logeros determinen con una fuerte garantóa de seguridad cuál es el estado de cualquier transacción de Bitcoin, y su saldo actual, mientras se descarga solo una porción muy pequeña de la cadena de bloques completa.

The Merkle tree protocol is arguably essential to long-term sustainability. A "full node" in the Bitcoin network, one that stores and processes the entirety of every block, takes up about 15 GB of disk space in the Bitcoin network as of April 2014, and is growing by over a gigabyte per month. Currently, this is viable for some desktop computers and not phones, and later on in the future only businesses and hobbyists will be able to participate. A protocol known as "simplified payment verification" (SPV) allows for another class of nodes to exist, called "light nodes", which download the block headers, verify the proof of work on the block headers, and then download only the "branches" associated with transactions that are relevant to them. This allows light nodes to determine with a strong guarantee of security what the status of any Bitcoin transaction, and their current balance, is while downloading only a very small portion of the entire blockchain.

### Alternative Blockchain Applications
### Aplicaciones alternativas de la cadena de bloques

La ide de tomar la subyacente idea de la cadena de bloques y aplicarla a otros conceptos también tiene una larga historia. Em 2005, Nick Szabo presenta el concepto de títulos de propiedad seguros con autoridad del propietario

The idea of taking the underlying blockchain idea and applying it to other concepts also has a long history. In 2005, Nick Szabo came out with the concept of secure property titles with owner authority,[<sup>[14]</sup>](http://nakamotoinstitute.org/secure-property-titles) 

un documento que describe como "nuevos avances en la tecnología de bases de datos replicadas" permitirán un sistema basado en cadenas de bloques para almacenar un regustro de quién posee qué tierra, creando un marco elaborado que incluye conceptos como homestanding, posesión adversa y el impuesto Gregoriano a la tierra. Sin embargo, lamentablemente no existía un sistema de base de datos replicado eficaz disponible en ese momento, por lo que el protocolo nunca se implementó en la práctica. Sin embargo, después de 2009, una vez que se desarrollói el consenso descentralizado de Bitcoin, rápidamente comenzaron a surgir varias aplicaciones alternativas.

a document describing how "new advances in replicated database technology" will allow for a blockchain-based system for storing a registry of who owns what land, creating an elaborate framework including concepts such as homesteading, adverse possession and Georgian land tax. However, there was unfortunately no effective replicated database system available at the time, and so the protocol was never implemented in practice. After 2009, however, once Bitcoin's decentralized consensus was developed a number of alternative applications rapidly began to emerge.

* **Namecoin** - creado en 2010, Namecoin 

es una base de datos descentralizada de registro de nombres. En protocolos descentralizados como Tor, Bitcoin y BitMessage, debe haber alguna manera de identificar cuentas para que otras personas puedan interactuar con ellas, pero en todsa las soluciones existentes, el único tipo de identificador dispinible es un hash pseudoaleatorio como `1LW79wp5ZBqaHW1jL5TCiBCrhQYtHagUWy`. Idealmente, alguien podría tener una cuenta con un nombre como "george". Sin embargo, el problema es que si una persona puede crear una cuenta llamada "george", entonces otra persona puede usar el mismo proceso para registrar "george" para sí mismo y suplantarlo La única solución es un paradigma de primer solicitante, donde el primero que registra tiene éxito y el segundo falla, un problema perfectamente adecuado para el protocolo de consenso de Bitcoin. Namecoin es la implementación más antigua y exitosa de un sistema de registro de nombres que utiliza dicha idea.

* **Namecoin** - created in 2010, Namecoin[<sup>[5]</sup>](https://namecoin.org/) is best described as a decentralized name registration database. In decentralized protocols like Tor, Bitcoin and BitMessage, there needs to be some way of identifying accounts so that other people can interact with them, but in all existing solutions the only kind of identifier available is a pseudorandom hash like `1LW79wp5ZBqaHW1jL5TCiBCrhQYtHagUWy`. Ideally, one would like to be able to have an account with a name like "george". However, the problem is that if one person can create an account named "george" then someone else can use the same process to register "george" for themselves as well and impersonate them. The only solution is a first-to-file paradigm, where the first registerer/registrar succeeds and the second fails - a problem perfectly suited for the Bitcoin consensus protocol. Namecoin is the oldest, and most successful, implementation of a name registration system using such an idea.

* **Colored coins** - el propósito de las monedas de colores (colored coins)[<sup>[3]</sup>]
* **Colored coins** - the purpose of colored coins[<sup>[3]</sup>]

El propósito es servir como un protocolo para permitir a las personas crear sus propias monedas digitales, o en el importante caso trivial de una moneda con unidad, fichas digitales (tokens), en la cadena de bloques de Bitcoin. En el protocolo de monedas de colores, uno "emite" una nueva moneda asignando públicamente un color a un Bitcoin UTXO específico, y el protocolo define recursivamente el color de otro UTXO para que sea el mismo color de las entradas que envió la transacción que los creó (se aplican algunas reglas especiales en el caso de las entradas de color mixto). Esto permite a los usuarios mantener carteras que solo contengan UTXO para un color específico y enviarlas de forma similar a las bitcoin regulares, retricediendo a través de la cadena de bloques para determunar el color de cualquier UTXO que reciban.


(https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit) is to serve as a protocol to allow people to create their own digital currencies - or, in the important trivial case of a currency with one unit, digital tokens, on the Bitcoin blockchain. In the colored coins protocol, one "issues" a new currency by publicly assigning a color to a specific Bitcoin UTXO, and the protocol recursively defines the color of other UTXO to be the same as the color of the inputs that the transaction creating them spent (some special rules apply in the case of mixed-color inputs). This allows users to maintain wallets containing only UTXO of a specific color and send them around much like regular bitcoins, backtracking through the blockchain to determine the color of any UTXO that they receive.

* **Metamonedas** - la idea detrás de un metacoin es tener un protocolo que viva sobre Bitcoin, usando transacciones de Bitcoin para almacenar transacciones de metacoin pero teniendo una función de transición de estado diferente, `APLICAR`. Como el protocolo de metacoin no puede evitar que aparezcan transacciones de metacoin no válidas en la cadena de bloques de Bitcoin, se agrega una regla que si `APLICAR(S, TX)` devuelve un error, el protocolo determinado es `APLICAR(S, TX) = S`. Esto propirciona un sencillo mecanismo para crear un protocolo de criptomoneda arbitrario, potencialmente con características avanzadas que no pueden implementarse dentro de Bitcoin, pero con un costo de desarrollo muy bajo ya que las complejidades de la mineróa y la interconexión ya se manejan con el protocolo Bitcoin. Los metacoins se han utilizado para implementar algunas clases de contratos financieros, registro de nombres e intercambio descentralizado.

* **Metacoins** - the idea behind a metacoin is to have a protocol that lives on top of Bitcoin, using Bitcoin transactions to store metacoin transactions but having a different state transition function, `APPLY'`. Because the metacoin protocol cannot prevent invalid metacoin transactions from appearing in the Bitcoin blockchain, a rule is added that if `APPLY'(S,TX)` returns an error, the protocol defaults to `APPLY'(S,TX) = S`. This provides an easy mechanism for creating an arbitrary cryptocurrency protocol, potentially with advanced features that cannot be implemented inside of Bitcoin itself, but with a very low development cost since the complexities of mining and networking are already handled by the Bitcoin protocol. Metacoins have been used to implement some classes of financial contracts, name registration and decentralized exchange.

Por lo tanto, en general, hay dos enfoques para construir un protocolo de conseso: construir una red independiente y construir un protocolo sobre Bitcoin. El primer enfoque, si bien es razonablemente exitoso en el caso de aplicaciones como Namecoin, es dificil de implementar; cada implementación individual necesita arrancar una cadena de bloques independiente, así como construir y probar todos los códigos necesarios de transacción de estado y red. Además, predecimos que el conjunto de aplicaciones para tecnología de conseso descentralizado seguirá una distribución de ley de poder donde la gran mayoría de las aplicaciones serían demasiado pequeñas para garantizar su propua cadena de bloques, y notamos que existen grandes clases de aplicaciones descentralizadas, particularmente descentralizadas y autónomas, que necesitan interactuar entre ellas.

Thus, in general, there are two approaches toward building a consensus protocol: building an independent network, and building a protocol on top of Bitcoin. The former approach, while reasonably successful in the case of applications like Namecoin, is difficult to implement; each individual implementation needs to bootstrap an independent blockchain, as well as building and testing all of the necessary state transition and networking code. Additionally, we predict that the set of applications for decentralized consensus technology will follow a power law distribution where the vast majority of applications would be too small to warrant their own blockchain, and we note that there exist large classes of decentralized applications, particularly decentralized autonomous organizations, that need to interact with each other.

Por otro lado, el enfoque basado en Bitcoin, tiene el inconveniente de que no hereda las características simplidicadas de verificación de pagos de Bitcoin. SPV funciona para Bitcoin porque puede usar la profundidad de la cadena de bloque como un proxy de validez. En algún punto, una vez que los antepasados de una transacción se remontan lo suficiente, es seguro decir que fueron legítimamente parte del estado. Los meta protocolos de Blockchain, por otro lado, no pueden obligar al blockchain a no incluir transacciones que no son válidas dentro del contexto de sus propios protocolos. Por lo tanto, una implementación de meta-protocolo SPV completamente segura necesitaría escanear hacia atrás hasta el comienzo de una cadena de bloques de Bitcoin para determinar si ciertas transacciones son válidas o no. Actualmente, todas las implementaciones "ligeras" de meta-protocolos basados en Bitcoin se basan en un servidor confiable para proporcionar los datos, posiblemente un resultado muy poco óptimo especialmente cuando uno de los propóditos principales de una criptomoneda es eleminar la necesidad de confianza.

The Bitcoin-based approach, on the other hand, has the flaw that it does not inherit the simplified payment verification features of Bitcoin. SPV works for Bitcoin because it can use blockchain depth as a proxy for validity; at some point, once the ancestors of a transaction go far enough back, it is safe to say that they were legitimately part of the state. Blockchain-based meta-protocols, on the other hand, cannot force the blockchain not to include transactions that are not valid within the context of their own protocols. Hence, a fully secure SPV meta-protocol implementation would need to backward scan all the way to the beginning of the Bitcoin blockchain to determine whether or not certain transactions are valid. Currently, all "light" implementations of Bitcoin-based meta-protocols rely on a trusted server to provide the data, arguably a highly suboptimal result especially when one of the primary purposes of a cryptocurrency is to eliminate the need for trust.

### Scripting
### Escritura

Incluso sin ninguna extensión, el protocolo de Bitcoin en realidad facilita una versión débil del concepto de "contratos inteligentes". UTXO en Bitcoin puede ser propiedad no solo de una clave pública, sino también de una secuencia de comandos más complicada expresada en un lenguaje de programación simple basado en la pila. En este paradigma, el gasto de una transacción que UTXO debe proporcionar datos que satisfagan el script. De hecho, incluso el mecanismo de propiedad de la llava pública básica se implementa a través de un script: el script toma una firma de curva elíptica

como entrada, verificandola contra la transacción y la dirección que posee el UTXO, y devuelve 1 si la verificación es exitosa y 0 en caso contrario. Existen otros scripts más complicados para varios casos de uso adicionales. Por ejemplo, uno puede contruir un script que requiere la firma de dos de las tres claves privadas para validad ("multigrado"), una configuración útil para cuentas corporativas, cuentas de ahorro seguras y algunas situaciones de escoltas de comerciantes. Los scripts también pueden usarse para pagar recompensas por soluciones a problemas de cómputo, y uno puede incluso contruir un script que diga algo como "este Bitcoin UTXO es tuyo si puedes proporcionar una prueva de SPV de que enviaste un Dodgecoin

transacción de esta denominación para mi", lo que permite esencialmente el intercambio descentralizado de criptomonedas cruzadas.

Even without any extensions, the Bitcoin protocol actually does facilitate a weak version of a concept of "smart contracts". UTXO in Bitcoin can be owned not just by a public key, but also by a more complicated script expressed in a simple stack-based programming language. In this paradigm, a transaction spending that UTXO must provide data that satisfies the script. Indeed, even the basic public key ownership mechanism is implemented via a script: the script takes an elliptic curve signature[<sup>[15]</sup>](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) as input, verifies it against the transaction and the address that owns the UTXO, and returns 1 if the verification is successful and 0 otherwise. Other, more complicated, scripts exist for various additional use cases. For example, one can construct a script that requires signatures from two out of a given three private keys to validate ("multisig"), a setup useful for corporate accounts, secure savings accounts and some merchant escrow situations. Scripts can also be used to pay bounties for solutions to computational problems, and one can even construct a script that says something like "this Bitcoin UTXO is yours if you can provide an SPV proof that you sent a Dogecoin[<sup>[16]</sup>](http://dogecoin.com/) transaction of this denomination to me", essentially allowing decentralized cross-cryptocurrency exchange.

Sin embargo, el lenguaje de scripting implementado en Bitcoun tiene varias limitaciones importantes:

However, the scripting language as implemented in Bitcoin has several important limitations:

* **Falta e completitud Turning (Integridad Turning)** - 
Es decir, si bien existe un gran subconjunto de computación compatible con el lenguaje de scripting de Bitcoin, no es compatibla con casi nada. La categoría principal faltante son los bucles. Esto se hace para evitar bucles infinitos durante la verificación de transacciones; teóricamente es un obstáculo superable para los programadores de scripts, ya que cualquier bucle puede simularse simplemente repitiendo el código subyacente, muchas veces con una instrucción if, pero conduce a scripts que son muy ineficientes en el uso del espacio. Por ejemplo, implementar un algoritmo 

alternativo de firma de curva elíptica probablemente requeriría 256 rondas de multiplicación repetidas, todas individualmente incluidas en el código. 

* **Lack of Turing-completeness** - that is to say, while there is a large subset of computation that the Bitcoin scripting language supports, it does not nearly support everything. The main category that is missing is loops. This is done to avoid infinite loops during transaction verification; theoretically it is a surmountable obstacle for script programmers, since any loop can be simulated by simply repeating the underlying code many times with an if statement, but it does lead to scripts that are very space-inefficient. For example, implementing an alternative elliptic curve signature algorithm[<sup>[15]</sup>](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) would likely require 256 repeated multiplication rounds all individually included in the code.

* **Valor ciego** - 
No hay forma que un script UTXO proporciona control detallado sobre la cantidad que puede retirarse. Por ejemplo, un caso de uso poderoso de un contrato sería un contrato de cobertura, donde A y B ponen $ 1000 en BTC y después de 30 días el script envía $ 1,000 en BTC a A y el resto a B. Esto requeriría un oráculo para determinar el valor de 1 BTC en USD, pero incluso asó es una mejora masiva en términos de confianza y requisitos de infraestructura sobre las soluciones toralmente centralizadas que están disponibles ahora. Sin embargo, debido a que UTXO son todo o nada, la única manera de lograrlo es a través del truco muy ineficiente de tener muchos UTXO de diferentes denominaciones (por ejemplo, un UTXO de 2 para cada k hasta 30) y teniendo O que elige qué UTXO enviar a A y cuál a B.

* **Value-blindness** - there is no way for a UTXO script to provide fine-grained control over the amount that can be withdrawn. For example, one powerful use case of an oracle contract would be a hedging contract, where A and B put in $1000 worth of BTC and after 30 days the script sends $1,000 worth of BTC to A and the rest to B. This would require an oracle to determine the value of 1 BTC in USD<sup>[Note 3]</sup>, but even then it is a massive improvement in terms of trust and infrastructure requirement over the fully centralized solutions that are available now. However, because UTXO are all-or-nothing, the only way to achieve this is through the very inefficient hack of having many UTXO of varying denominations (eg. one UTXO of 2<sup>k</sup> for every k up to 30) and having O pick which UTXO to send to A and which to B.


* **Falta de estado** - UTXO puede gastarse o no gastarse; no hay oportunidad para contratos o scripts de varias etapas que mantengan cualquier otro estado interno más allá de eso. Esto hace que sea difícil hacer contratos de opciones de múltiples etapas, pfertas de intercambio descentralizadas o protocolos de compomso criptográgico de dos etapas (necesarios para recompensas computacionales seguras). También significa que UTXO sólo se puede usar para crear contratos simples, únicos y no más complejos como contratos "de estado", como organizaciones descentralizadas, y hace que los meta-protocolos sean difíciles de implementar. El estado binario combinado con la ceguera de valor también significa que otra aplicación importante, límites de extracción, es imposible.

* **Lack of state** - UTXO can either be spent or unspent; there is no opportunity for multi-stage contracts or scripts which keep any other internal state beyond that. This makes it hard to make multi-stage options contracts, decentralized exchange offers or two-stage cryptographic commitment protocols (necessary for secure computational bounties). It also means that UTXO can only be used to build simple, one-off contracts and not more complex "stateful" contracts such as decentralized organizations, and makes meta-protocols difficult to implement. Binary state combined with value-blindness also mean that another important application, withdrawal limits, is impossible.

* **Ceguera Blockchain** - Los UTXO están cegados a ciertos datos de blockchain como nonce y hash de bloque anterior. Esto limita severamente las aplicaciones en los juegos de azar, y varias otras categorías, al privar al lenguaje de scripting de una fuente potencialmente valiosa de aleatoriedad.

* **Blockchain-blindness** - UTXO are blind to certain blockchain data such as the nonce and previous block hash. This severely limits applications in gambling, and several other categories, by depriving the scripting language of a potentially valuable source of randomness.

Por lo tanto, vemos tres enfoques para construir aplicaciones avanzadas sobre la criptomoneda: construyendo un blockchain nuevo, usando scripting encima de Bitcoin, y construyendo un meta-protocolo sobre Bitcoin. La construcción de una nueva cadena de bloques permite una libertad ilimitada en la creación de un conjunto de características, pero a costa del tiempo de desarrollo, el esfuerzo inicial y la seguridad. El uso de scripting es fácil de implementar y estandarizar, pero tiene capacidades muy limitadas, y los meta protocolos, aunque son fáciles, adolecen de fallas en la escalabilidad. Con Ethereum, se intenta construir un framework alternativo que proporcione ganancias aún mayores en la facilidad de desarrollo así como una propiedad de clientes ligeros más fuerte, permiteiendo que las aplicaciones compartan el entorno económico y la seguridad del blockchain.

Thus, we see three approaches to building advanced applications on top of cryptocurrency: building a new blockchain, using scripting on top of Bitcoin, and building a meta-protocol on top of Bitcoin. Building a new blockchain allows for unlimited freedom in building a feature set, but at the cost of development time, bootstrapping effort and security. Using scripting is easy to implement and standardize, but is very limited in its capabilities, and meta-protocols, while easy, suffer from faults in scalability. With Ethereum, we intend to build an alternative framework that provides even larger gains in ease of development as well as even stronger light client properties, while at the same time allowing applications to share an economic environment and blockchain security.

## Ethereum

## Ethereum

La intención de Ethereum es crear un protocolo alternativo para consturir aplicaciones descentralizadas, proveyendo un sistema de compensación diferente que se cree será muy útil para una larga clase de aplicaciones descentralizadas, con particular énfasis en situaciones donde un rápido tiempo de desarrollo, seguridad para aplicaciones pequeñas y poco utilizadas, y la habilidad de interacción muy eficiente de distintas aplicaciones, son importantes. Ethereum permite esto através de la construcción de lo que en esencia es la última capa de abstración: una cadena de bloques con un lenguaje de programación con completitúd Turing, permitiendo a cualquiera escribir contratos inteligentes y aplicaciones descentralizadas donde ellos pueden crear sus propios reglas arbitrarias para posesión, formatos de transación y funciones de transición de estado. Una versión primitiva de Namecoin puede ser escrita como dos líneas de código,

y otros protocolos como monedas y sistemas de reputación pueden ser construidos en menos de veinte líneaas de código- Contratos inteligentes, "cajas" criiptográficas que contienen valor y sólo pueden ser desbloqueadas mediante el complimiento de ciertas condiciones, pueden además ser construidas sobre la plataforma, con un poder mayot que el ofrecido por el scripting Bitcoin debido al poder que añaden la completitúd de Turning, conciencia de valor, conciencia de cadena de bloques y el estado.

The intent of Ethereum is to create an alternative protocol for building decentralized applications, providing a different set of tradeoffs that we believe will be very useful for a large class of decentralized applications, with particular emphasis on situations where rapid development time, security for small and rarely used applications, and the ability of different applications to very efficiently interact, are important. Ethereum does this by building what is essentially the ultimate abstract foundational layer: a blockchain with a built-in Turing-complete programming language, allowing anyone to write smart contracts and decentralized applications where they can create their own arbitrary rules for ownership, transaction formats and state transition functions. A bare-bones version of Namecoin can be written in two lines of code, 

and other protocols like currencies and reputation systems can be built in under twenty. Smart contracts, cryptographic "boxes" that contain value and only unlock it if certain conditions are met, can also be built on top of the platform, with vastly more power than that offered by Bitcoin scripting because of the added powers of Turing-completeness, value-awareness, blockchain-awareness and state.

### Ethereum Accounts
## Cuentas Ethereum

En Ethereum, el estado está hecho de objetos llamados "cuentas", cada cuenta tiene una dirección de 20 bytes y transiciones de estado siendo transferencias directas de valor e información entre cuentas. Una cuenta Ethereum contiene cuatro campos:

* El **nonce**, un contador usado para asegurar que cada transación se procese sólo una vez
* El actual **balance de ether** de la cuenta
* El **código de contrato**, si existe
* El **almacenamiento** de la cuenta (vacío por defecto)

In Ethereum, the state is made up of objects called "accounts", with each account having a 20-byte address and state transitions being direct transfers of value and information between accounts. An Ethereum account contains four fields:

* The **nonce**, a counter used to make sure each transaction can only be processed once
* The account's current **ether balance**
* The account's **contract code**, if present
* The account's **storage** (empty by default)

"Ether" es el principal crypto-combustible de Ethereum, y es utilizado para pagar las cuotas de transaciones. En general, existen dos tipos de cuentas: **cuentas de propiedad externa** controladas por llaves privadas, y **cuentas contrato** controladas por su código de contrato. Una cuenta de propiedad externa no tiene código, y uno puede enviar mensajes desde una cuenta de propiedad externa mediante la creación y firma de una transacción. En una cuenta contrato, cada vez que la cuenta contrato recive mensajes su código se activa, permitiendole leer y escribir en el almacenamiento interno y enviar otros mensajes o crear contratos en turno.

"Ether" is the main internal crypto-fuel of Ethereum, and is used to pay transaction fees. In general, there are two types of accounts: **externally owned accounts**, controlled by private keys, and **contract accounts**, controlled by their contract code. An externally owned account has no code, and one can send messages from an externally owned account by creating and signing a transaction. In a contract account, every time the contract account receives a message its code activates, allowing it to read and write to internal storage and send other messages or create contracts in turn.

Observa que los "contratos" en Ethereum no deberían ser vistos como algo que debería "cumplirse" o "ser cumplido"; más bien, pueden pensarse como "agentes autónomos" que viven dentro del entorno de ejecución de Ethereum, siempre ejecutando una pieza específica de código cuando es "tocada" por un mensaje o transacción, y obteniendo control directo sobre su propio balance ether y su propio almacenamiento llave/valor para guardar registro de variables persistentes.

Note that "contracts" in Ethereum should not be seen as something that should be "fulfilled" or "complied with"; rather, they are more like "autonomous agents" that live inside of the Ethereum execution environment, always executing a specific piece of code when "poked" by a message or transaction, and having direct control over their own ether balance and their own key/value store to keep track of persistent variables.

### Messages and Transactions
### Mensajes y Transacciones

El término "transacción" es utilizado en Ethereum para referirse al paquete de datos firmado que guarda un mensaje para ser enviado desde una cuenta de propiedad externa. Las transacciones contienen:

* el receptor del mensaje;
* una firma identificando al emisor;
* la cantidad de ether a transferir desde el emisor al receptor;
* un opcional campo de datos;
* un valor `GASINICIAL`, representando el máximo número de pasos computacionales que la transacción tiene permitido tomar; y
* un valor `PRECIOGAS`, representando la tarifa que el emisor paga por paso computacional.

The term "transaction" is used in Ethereum to refer to the signed data package that stores a message to be sent from an externally owned account. Transactions contain:

* the recipient of the message;
* a signature identifying the sender;
* the amount of ether to transfer from the sender to the recipient; 
* an optional data field;
* a `STARTGAS` value, representing the maximum number of computational steps the transaction execution is allowed to take; and
* a `GASPRICE` value, representing the fee the sender pays per computational step.


Los primeros tres son campos estadar esperados en cualquier criptomoneda. El campo datos no tiene una función por defecto, pero la máquina virtual tiene un código de operación mediante el cual un contrato puede accesar a los datos. Como ejemplo de caso de uso, si el contrato está funcionando como un servicio de registro de dominio, entonces puede querer interpretar los datos los datos recibidos como dos "campos", el primer campo siendo el dominio a registrar y el segundo campo siendo la dirección IP a registrar. El contrato leería estos valores desde los datos del mensaje y los colocaría adecuandamente en el almacenamiento.

The first three are standard fields expected in any cryptocurrency. The data field has no function by default, but the virtual machine has an opcode with which a contract can access the data. As an example use case, if a contract is functioning as an on-blockchain domain registration service, then it may wish to interpret the data being passed to it as containing two "fields", the first field being a domain to register and the second field being the IP address to register it to. The contract would read these values from the message data and appropriately place them in storage.

Los campos `GASINICIAL` y `PRECIOGAS` son cruciales para el modelo de servicio anti denegación.

Con la finalidad de prevenir loops infinitos accidentales u hostiles, así como otro código computacional de desperdicio, cada transacción require poner límite a los pasos computacionales de ejecución de código puede utilizar. La unidad fundamental de computación es "gas"; usualmente, un paso computacional cuesta 1 gas, pero algunas operaciones tienen un costo mayor de gas porque son más caros computacionalmente, o incrementa la cantidad de datos que debe ser guardado como parte del estado. También existe una tarifa de 5 gas por cada byte en los datos de transacción. LA intención del sistema de tarifas, es requerir que un atacante pague proporcionalmente por cada recurso que consuma, incluyendo cómputación, ancho de banda y almacenamiento; por lo tanto, cualquier transacción que conduzca a que la red consuma una mayor cantidad de cualquiera de estos recursos debe tener una tarifa de gas más o menos proporcional al incremento.

The `STARTGAS` and `GASPRICE` fields are crucial for Ethereum's anti-denial-of-service model.[<sup>[17]</sup>](https://en.wikipedia.org/wiki/Denial-of-service_attack) In order to prevent accidental or hostile infinite loops or other computational wastage in code, each transaction is required to set a limit to how many computational steps of code execution it can use. The fundamental unit of computation is "gas"; usually, a computational step costs 1 gas, but some operations cost higher amounts of gas because they are more computationally expensive, or increase the amount of data that must be stored as part of the state. There is also a fee of 5 gas for every byte in the transaction data. The intent of the fee system is to require an attacker to pay proportionately for every resource that they consume, including computation, bandwidth and storage; hence, any transaction that leads to the network consuming a greater amount of any of these resources must have a gas fee roughly proportional to the increment.

### Messages
### Mensajes

Los contratos tienen la habilidad de enviar "mensajes" a otros contratos. Los mensajes son objetos virtuales nunca serializados que existen sólo en el entorno de ejecición de Ethereum. Un mensaje contiene:

* el emisor del mensaje (implícito);
* el receptor del mensaje;
* la cantidad de ether a transferir junto al mensaje
* un campo opcional de datos; y
* un valor `GASINICIAL`

Escencialmen

Contracts have the ability to send "messages" to other contracts. Messages are virtual objects that are never serialized and exist only in the Ethereum execution environment. A message contains:

* the sender of the message (implicit);
* the recipient of the message;
* the amount of ether to transfer alongside the message;
* an optional data field; and
* a `STARTGAS` value.

Essentially, a message is like a transaction, except it is produced by a contract and not an external actor. A message is produced when a contract currently executing code executes the `CALL` opcode, which produces and executes a message. Like a transaction, a message leads to the recipient account running its code. Thus, contracts can have relationships with other contracts in exactly the same way that external actors can.

Note that the gas allowance assigned by a transaction or contract applies to the total gas consumed by that transaction and all sub-executions. For example, if an external actor A sends a transaction to B with 1,000 gas; B consumes 600 gas before sending a message to C; and the internal execution of C consumes 300 gas before returning; then B can spend another 100 gas before running out of gas.

### Ethereum State Transition Function

![ethertransition.png](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/ethertransition.png)

The Ethereum state transition function, `APPLY(S,TX) -> S'` can be defined as follows:
ñ
1. Check if the transaction is well-formed (ie. has the right number of values), the signature is valid, and the nonce matches the nonce in the sender's account. If not, return an error.
2. Calculate the transaction fee as `STARTGAS * GASPRICE`, and determine the sending address from the signature. Subtract the fee from the sender's account balance and increment the sender's nonce. If there is not enough balance to spend, return an error.
3. Initialize `GAS = STARTGAS`, and take off a certain quantity of gas per byte to pay for the bytes in the transaction.
4. Transfer the transaction value from the sender's account to the receiving account. If the receiving account does not yet exist, create it. If the receiving account is a contract, run the contract's code either to completion or until the execution runs out of gas.
5. If the value transfer failed because the sender did not have enough money, or the code execution ran out of gas, revert all state changes except the payment of the fees, and add the fees to the miner's account.
6. Otherwise, refund the fees for all remaining gas to the sender, and send the fees paid for gas consumed to the miner.

For example, suppose that the contract's code is:

    if !self.storage[calldataload(0)]:
        self.storage[calldataload(0)] = calldataload(32)

Note that in reality the contract code is written in the low-level EVM code; this example is written in Serpent, one of our high-level languages, for clarity, and can be compiled down to EVM code. Suppose that the contract's storage starts off empty, and a transaction is sent with 10 ether value, 2000 gas, 0.001 ether gasprice, and 64 bytes of data, with bytes 0-31 representing the number `2` and bytes 32-63 representing the string `CHARLIE`.<sup>[Note 4]</sup> The process for the state transition function in this case is as follows:

1. Check that the transaction is valid and well formed.
2. Check that the transaction sender has at least 2000 * 0.001 = 2 ether. If they do, then subtract 2 ether from the sender's account.
3. Initialize gas = 2000; assuming the transaction is 170 bytes long and the byte-fee is 5, subtract 850 so that there is 1150 gas left.
3. Subtract 10 more ether from the sender's account, and add it to the contract's account.
4. Run the code. In this case, this is simple: it checks if the contract's storage at index `2` is used<sup>[Note 5]</sup>, notices that it is not, and so it sets the storage at index `2` to the value `CHARLIE`. Suppose this takes 187 gas, so the remaining amount of gas is 1150 - 187 = 963.
5. Add 963 * 0.001 = 0.963 ether back to the sender's account, and return the resulting state.

If there was no contract at the receiving end of the transaction, then the total transaction fee would simply be equal to the provided `GASPRICE` multiplied by the length of the transaction in bytes, and the data sent alongside the transaction would be irrelevant.

Note that messages work equivalently to transactions in terms of reverts: if a message execution runs out of gas, then that message's execution, and all other executions triggered by that execution, revert, but parent executions do not need to revert. This means that it is "safe" for a contract to call another contract, as if A calls B with G gas then A's execution is guaranteed to lose at most G gas. Finally, note that there is an opcode, `CREATE`, that creates a contract; its execution mechanics are generally similar to `CALL`, with the exception that the output of the execution determines the code of a newly created contract.

### Code Execution

The code in Ethereum contracts is written in a low-level, stack-based bytecode language, referred to as "Ethereum virtual machine code" or "EVM code". The code consists of a series of bytes, where each byte represents an operation. In general, code execution is an infinite loop that consists of repeatedly carrying out the operation at the current program counter (which begins at zero) and then incrementing the program counter by one, until the end of the code is reached or an error or `STOP` or `RETURN` instruction is detected. The operations have access to three types of space in which to store data:

* The **stack**, a last-in-first-out container to which values can be pushed and popped
* **Memory**, an infinitely expandable byte array
* The contract's long-term **storage**, a key/value store. Unlike stack and memory, which reset after computation ends, storage persists for the long term.

The code can also access the value, sender and data of the incoming message, as well as block header data, and the code can also return a byte array of data as an output.

The formal execution model of EVM code is surprisingly simple. While the Ethereum virtual machine is running, its full computational state can be defined by the tuple `(block_state, transaction, message, code, memory, stack, pc, gas)`, where `block_state` is the global state containing all accounts and includes balances and storage. At the start of every round of execution, the current instruction is found by taking the `pc`th (Program Counter) byte of `code` (or 0 if `pc >= len(code)`), and each instruction has its own definition in terms of how it affects the tuple. For example, `ADD` pops two items off the stack and pushes their sum, reduces `gas` by 1 and increments `pc` by 1, and `SSTORE` pops the top two items off the stack and inserts the second item into the contract's storage at the index specified by the first item. Although there are many ways to optimize Ethereum virtual machine execution via just-in-time compilation, a basic implementation of Ethereum can be done in a few hundred lines of code.

### Blockchain and Mining

![apply_block_diagram.png](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/apply_block_diagram.png)

The Ethereum blockchain is in many ways similar to the Bitcoin blockchain, although it does have some differences. The main difference between Ethereum and Bitcoin with regard to the blockchain architecture is that, unlike Bitcoin, Ethereum blocks contain a copy of both the transaction list and the most recent state. Aside from that, two other values, the block number and the difficulty, are also stored in the block. The basic block validation algorithm in Ethereum is as follows:

1. Check if the previous block referenced exists and is valid.
2. Check that the timestamp of the block is greater than that of the referenced previous block and less than 15 minutes into the future
3. Check that the block number, difficulty, transaction root, uncle root and gas limit (various low-level Ethereum-specific concepts) are valid.
4. Check that the proof of work on the block is valid.
5. Let `S[0]` be the state at the end of the previous block.
6. Let `TX` be the block's transaction list, with `n` transactions. For all `i` in `0...n-1`, set `S[i+1] = APPLY(S[i],TX[i])`. If any application returns an error, or if the total gas consumed in the block up until this point exceeds the `GASLIMIT`, return an error. 
7. Let `S_FINAL` be `S[n]`, but adding the block reward paid to the miner.
8. Check if the Merkle tree root of the state `S_FINAL` is equal to the final state root provided in the block header. If it is, the block is valid; otherwise, it is not valid.

The approach may seem highly inefficient at first glance, because it needs to store the entire state with each block, but in reality efficiency should be comparable to that of Bitcoin. The reason is that the state is stored in the tree structure, and after every block only a small part of the tree needs to be changed. Thus, in general, between two adjacent blocks the vast majority of the tree should be the same, and therefore the data can be stored once and referenced twice using pointers (ie. hashes of subtrees). A special kind of tree known as a "Patricia tree" is used to accomplish this, including a modification to the Merkle tree concept that allows for nodes to be inserted and deleted, and not just changed, efficiently.  Additionally, because all of the state information is part of the last block, there is no need to store the entire blockchain history - a strategy which, if it could be applied to Bitcoin, can be calculated to provide 5-20x savings in space.

A commonly asked question is "where" contract code is executed, in terms of physical hardware. This has a simple answer: the process of executing contract code is part of the definition of the state transition function, which is part of the block validation algorithm, so if a transaction is added into block `B` the code execution spawned by that transaction will be executed by all nodes, now and in the future, that download and validate block `B`. 

## Applications

In general, there are three types of applications on top of Ethereum. The first category is financial applications, providing users with more powerful ways of managing and entering into contracts using their money. This includes sub-currencies, financial derivatives, hedging contracts, savings wallets, wills, and ultimately even some classes of full-scale employment contracts. The second category is semi-financial applications, where money is involved but there is also a heavy non-monetary side to what is being done; a perfect example is self-enforcing bounties for solutions to computational problems. Finally, there are applications such as online voting and decentralized governance that are not financial at all.

### Token Systems

On-blockchain token systems have many applications ranging from sub-currencies representing assets such as USD or gold to company stocks, individual tokens representing smart property, secure unforgeable coupons, and even token systems with no ties to conventional value at all, used as point systems for incentivization. Token systems are surprisingly easy to implement in Ethereum. The key point to understand is that a currency, or token system, is just a database with one operation: subtract X units from A and give X units to B, with the proviso that (i) A had at least X units before the transaction and (ii) the transaction is approved by A. All that it takes to implement a token system is to implement this logic into a contract.

The basic code for implementing a token system in Serpent looks as follows:

    def send(to, value):
        if self.storage[msg.sender] >= value:
            self.storage[msg.sender] = self.storage[msg.sender] - value
            self.storage[to] = self.storage[to] + value

This is essentially a literal implementation of the "banking system" state transition function described further above in this document. A few extra lines of code need to be added to provide for the initial step of distributing the currency units in the first place and a few other edge cases, and ideally a function would be added to let other contracts query for the balance of an address. But that's all there is to it. Theoretically, Ethereum-based token systems acting as sub-currencies can potentially include another important feature that on-chain Bitcoin-based meta-currencies lack: the ability to pay transaction fees directly in that currency. The way this would be implemented is that the contract would maintain an ether balance with which it would refund ether used to pay fees to the sender, and it would refill this balance by collecting the internal currency units that it takes in fees and reselling them in a constant running auction. Users would thus need to "activate" their accounts with ether, but once the ether is there it would be reusable because the contract would refund it each time.

### Financial derivatives and Stable-Value Currencies

Financial derivatives are the most common application of a "smart contract", and one of the simplest to implement in code. The main challenge in implementing financial contracts is that the majority of them require reference to an external price ticker; for example, a very desirable application is a smart contract that hedges against the volatility of ether (or another cryptocurrency) with respect to the US dollar, but doing this requires the contract to know what the value of ETH/USD is. The simplest way to do this is through a "data feed" contract maintained by a specific party (eg. NASDAQ) designed so that that party has the ability to update the contract as needed, and providing an interface that allows other contracts to send a message to that contract and get back a response that provides the price.

Given that critical ingredient, the hedging contract would look as follows:

1. Wait for party A to input 1000 ether.
2. Wait for party B to input 1000 ether.
3. Record the USD value of 1000 ether, calculated by querying the data feed contract, in storage, say this is $x.
4. After 30 days, allow A or B to "reactivate" the contract in order to send $x worth of ether (calculated by querying the data feed contract again to get the new price) to A and the rest to B.

Such a contract would have significant potential in crypto-commerce. One of the main problems cited about cryptocurrency is the fact that it's volatile; although many users and merchants may want the security and convenience of dealing with cryptographic assets, they may not wish to face that prospect of losing 23% of the value of their funds in a single day. Up until now, the most commonly proposed solution has been issuer-backed assets; the idea is that an issuer creates a sub-currency in which they have the right to issue and revoke units, and provide one unit of the currency to anyone who provides them (offline) with one unit of a specified underlying asset (eg. gold, USD). The issuer then promises to provide one unit of the underlying asset to anyone who sends back one unit of the crypto-asset. This mechanism allows any non-cryptographic asset to be "uplifted" into a cryptographic asset, provided that the issuer can be trusted.

In practice, however, issuers are not always trustworthy, and in some cases the banking infrastructure is too weak, or too hostile, for such services to exist. Financial derivatives provide an alternative. Here, instead of a single issuer providing the funds to back up an asset, a decentralized market of speculators, betting that the price of a cryptographic reference asset (eg. ETH) will go up, plays that role. Unlike issuers, speculators have no option to default on their side of the bargain because the hedging contract holds their funds in escrow. Note that this approach is not fully decentralized, because a trusted source is still needed to provide the price ticker, although arguably even still this is a massive improvement in terms of reducing infrastructure requirements (unlike being an issuer, issuing a price feed requires no licenses and can likely be categorized as free speech) and reducing the potential for fraud.

### Identity and Reputation Systems

The earliest alternative cryptocurrency of all, [Namecoin](http://namecoin.org/), attempted to use a Bitcoin-like blockchain to provide a name registration system, where users can register their names in a public database alongside other data. The major cited use case is for a [DNS](http://en.wikipedia.org/wiki/Domain_Name_System) system, mapping domain names like "bitcoin.org" (or, in Namecoin's case, "bitcoin.bit") to an IP address. Other use cases include email authentication and potentially more advanced reputation systems. Here is the basic contract to provide a Namecoin-like name registration system on Ethereum:

    def register(name, value):
        if !self.storage[name]:
            self.storage[name] = value

The contract is very simple; all it is is a database inside the Ethereum network that can be added to, but not modified or removed from. Anyone can register a name with some value, and that registration then sticks forever. A more sophisticated name registration contract will also have a "function clause" allowing other contracts to query it, as well as a mechanism for the "owner" (ie. the first registerer) of a name to change the data or transfer ownership. One can even add reputation and web-of-trust functionality on top.

### Decentralized File Storage

Over the past few years, there have emerged a number of popular online file storage startups, the most prominent being Dropbox, seeking to allow users to upload a backup of their hard drive and have the service store the backup and allow the user to access it in exchange for a monthly fee. However, at this point the file storage market is at times relatively inefficient; a cursory look at various [existing solutions](http://online-storage-service-review.toptenreviews.com/) shows that, particularly at the "uncanny valley" 20-200 GB level at which neither free quotas nor enterprise-level discounts kick in, monthly prices for mainstream file storage costs are such that you are paying for more than the cost of the entire hard drive in a single month. Ethereum contracts can allow for the development of a decentralized file storage ecosystem, where individual users can earn small quantities of money by renting out their own hard drives and unused space can be used to further drive down the costs of file storage. 

The key underpinning piece of such a device would be what we have termed the "decentralized Dropbox contract". This contract works as follows. First, one splits the desired data up into blocks, encrypting each block for privacy, and builds a Merkle tree out of it. One then makes a contract with the rule that, every N blocks, the contract would pick a random index in the Merkle tree (using the previous block hash, accessible from contract code, as a source of randomness), and give X ether to the first entity to supply a transaction with a simplified payment verification-like proof of ownership of the block at that particular index in the tree. When a user wants to re-download their file, they can use a micropayment channel protocol (eg. pay 1 szabo per 32 kilobytes) to recover the file; the most fee-efficient approach is for the payer not to publish the transaction until the end, instead replacing the transaction with a slightly more lucrative one with the same nonce after every 32 kilobytes.

An important feature of the protocol is that, although it may seem like one is trusting many random nodes not to decide to forget the file, one can reduce that risk down to near-zero by splitting the file into many pieces via secret sharing, and watching the contracts to see if each piece is still in some node's possession. If a contract is still paying out money, that provides a cryptographic proof that someone out there is still storing the file. 

### Decentralized Autonomous Organizations

The general concept of a "decentralized autonomous organization" is that of a virtual entity that has a certain set of members or shareholders which, perhaps with a 67% majority, have the right to spend the entity's funds and modify its code. The members would collectively decide on how the organization should allocate its funds. Methods for allocating a DAO's funds could range from bounties, salaries to even more exotic mechanisms such as an internal currency to reward work. This essentially replicates the legal trappings of a traditional company or nonprofit but using only cryptographic blockchain technology for enforcement. So far much of the talk around DAOs has been around the "capitalist" model of a "decentralized autonomous corporation" (DAC) with dividend-receiving shareholders and tradable shares; an alternative, perhaps described as a "decentralized autonomous community", would have all members have an equal share in the decision making and require 67% of existing members to agree to add or remove a member. The requirement that one person can only have one membership would then need to be enforced collectively by the group.

A general outline for how to code a DAO is as follows. The simplest design is simply a piece of self-modifying code that changes if two thirds of members agree on a change. Although code is theoretically immutable, one can easily get around this and have de-facto mutability by having chunks of the code in separate contracts, and having the address of which contracts to call stored in the modifiable storage. In a simple implementation of such a DAO contract, there would be three transaction types, distinguished by the data provided in the transaction:

* `[0,i,K,V]` to register a proposal with index `i` to change the address at storage index `K` to value `V`
* `[1,i]` to register a vote in favor of proposal `i`
* `[2,i]` to finalize proposal `i` if enough votes have been made

The contract would then have clauses for each of these. It would maintain a record of all open storage changes, along with a list of who voted for them. It would also have a list of all members. When any storage change gets to two thirds of members voting for it, a finalizing transaction could execute the change. A more sophisticated skeleton would also have built-in voting ability for features like sending a transaction, adding members and removing members, and may even provide for [Liquid Democracy](http://en.wikipedia.org/wiki/Delegative_democracy)-style vote delegation (ie. anyone can assign someone to vote for them, and assignment is transitive so if A assigns B and B assigns C then C determines A's vote). This design would allow the DAO to grow organically as a decentralized community, allowing people to eventually delegate the task of filtering out who is a member to specialists, although unlike in the "current system" specialists can easily pop in and out of existence over time as individual community members change their alignments.

An alternative model is for a decentralized corporation, where any account can have zero or more shares, and two thirds of the shares are required to make a decision. A complete skeleton would involve asset management functionality, the ability to make an offer to buy or sell shares, and the ability to accept offers (preferably with an order-matching mechanism inside the contract). Delegation would also exist Liquid Democracy-style, generalizing the concept of a "board of directors".

### Further Applications

**1. Savings wallets**. Suppose that Alice wants to keep her funds safe, but is worried that she will lose or someone will hack her private key. She puts ether into a contract with Bob, a bank, as follows:

* Alice alone can withdraw a maximum of 1% of the funds per day.
* Bob alone can withdraw a maximum of 1% of the funds per day, but Alice has the ability to make a transaction with her key shutting off this ability.
* Alice and Bob together can withdraw anything.

Normally, 1% per day is enough for Alice, and if Alice wants to withdraw more she can contact Bob for help. If Alice's key gets hacked, she runs to Bob to move the funds to a new contract. If she loses her key, Bob will get the funds out eventually. If Bob turns out to be malicious, then she can turn off his ability to withdraw.

**2. Crop insurance**. One can easily make a financial derivatives contract but using a data feed of the weather instead of any price index. If a farmer in Iowa purchases a derivative that pays out inversely based on the precipitation in Iowa, then if there is a drought, the farmer will automatically receive money and if there is enough rain the farmer will be happy because their crops would do well. This can be expanded to natural disaster insurance generally.

**3. A decentralized data feed**. For financial contracts for difference, it may actually be possible to decentralize the data feed via a protocol called "[SchellingCoin](http://blog.ethereum.org/2014/03/28/schellingcoin-a-minimal-trust-universal-data-feed/)". SchellingCoin basically works as follows: N parties all put into the system the value of a given datum (eg. the ETH/USD price), the values are sorted, and everyone between the 25th and 75th percentile gets one token as a reward. Everyone has the incentive to provide the answer that everyone else will provide, and the only value that a large number of players can realistically agree on is the obvious default: the truth. This creates a decentralized protocol that can theoretically provide any number of values, including the ETH/USD price, the temperature in Berlin or even the result of a particular hard computation.

**4. Smart multisignature escrow**. Bitcoin allows multisignature transaction contracts where, for example, three out of a given five keys can spend the funds. Ethereum allows for more granularity; for example, four out of five can spend everything, three out of five can spend up to 10% per day, and two out of five can spend up to 0.5% per day. Additionally, Ethereum multisig is asynchronous - two parties can register their signatures on the blockchain at different times and the last signature will automatically send the transaction.

**5. Cloud computing**. The EVM technology can also be used to create a verifiable computing environment, allowing users to ask others to carry out computations and then optionally ask for proofs that computations at certain randomly selected checkpoints were done correctly. This allows for the creation of a cloud computing market where any user can participate with their desktop, laptop or specialized server, and spot-checking together with security deposits can be used to ensure that the system is trustworthy (ie. nodes cannot profitably cheat). Although such a system may not be suitable for all tasks; tasks that require a high level of inter-process communication, for example, cannot easily be done on a large cloud of nodes. Other tasks, however, are much easier to parallelize; projects like SETI@home, folding@home and genetic algorithms can easily be implemented on top of such a platform.

**6. Peer-to-peer gambling**. Any number of peer-to-peer gambling protocols, such as Frank Stajano and Richard Clayton's [Cyberdice](http://www.cl.cam.ac.uk/~fms27/papers/2008-StajanoCla-cyberdice.pdf), can be implemented on the Ethereum blockchain. The simplest gambling protocol is actually simply a contract for difference on the next block hash, and more advanced protocols can be built up from there, creating gambling services with near-zero fees that have no ability to cheat.

**7. Prediction markets**. Provided an oracle or SchellingCoin, prediction markets are also easy to implement, and prediction markets together with SchellingCoin may prove to be the first mainstream application of [futarchy](http://hanson.gmu.edu/futarchy.html) as a governance protocol for decentralized organizations.

**8. On-chain decentralized marketplaces**, using the identity and reputation system as a base.

## Miscellanea And Concerns

### Modified GHOST Implementation

The "Greedy Heaviest Observed Subtree" (GHOST) protocol is an innovation first introduced by Yonatan Sompolinsky and Aviv Zohar in [December 2013](http://www.cs.huji.ac.il/~yoni_sompo/pubs/15/btc_scalability_full.pdf). The motivation behind GHOST is that blockchains with fast confirmation times currently suffer from reduced security due to a high stale rate - because blocks take a certain time to propagate through the network, if miner A mines a block and then miner B happens to mine another block before miner A's block propagates to B, miner B's block will end up wasted and will not contribute to network security. Furthermore, there is a centralization issue: if miner A is a mining pool with 30% hashpower and B has 10% hashpower, A will have a risk of producing a stale block 70% of the time (since the other 30% of the time A produced the last block and so will get mining data immediately) whereas B will have a risk of producing a stale block 90% of the time. Thus, if the block interval is short enough for the stale rate to be high, A will be substantially more efficient simply by virtue of its size. With these two effects combined, blockchains which produce blocks quickly are very likely to lead to one mining pool having a large enough percentage of the network hashpower to have de facto control over the mining process.

As described by Sompolinsky and Zohar, GHOST solves the first issue of network security loss by including stale blocks in the calculation of which chain is the "longest"; that is to say, not just the parent and further ancestors of a block, but also the stale descendants of the block's ancestor (in Ethereum jargon, "uncles") are added to the calculation of which block has the largest total proof of work backing it. To solve the second issue of centralization bias, we go beyond the protocol described by Sompolinsky and Zohar, and also provide block rewards to stales: a stale block receives 87.5% of its base reward, and the nephew that includes the stale block receives the remaining 12.5%. Transaction fees, however, are not awarded to uncles.

Ethereum implements a simplified version of GHOST which only goes down seven levels. Specifically, it is defined as follows:

* A block must specify a parent, and it must specify 0 or more uncles
* An uncle included in block B must have the following properties:
  * It must be a direct child of the kth generation ancestor of B, where 2 <= k <= 7.
  * It cannot be an ancestor of B
  * An uncle must be a valid block header, but does not need to be a previously verified or even valid block
  * An uncle must be different from all uncles included in previous blocks and all other uncles included in the same block (non-double-inclusion)
* For every uncle U in block B, the miner of B gets an additional 3.125% added to its coinbase reward and the miner of U gets 93.75% of a standard coinbase reward.

This limited version of GHOST, with uncles includable only up to 7 generations, was used for two reasons. First, unlimited GHOST would include too many complications into the calculation of which uncles for a given block are valid. Second, unlimited GHOST with compensation as used in Ethereum removes the incentive for a miner to mine on the main chain and not the chain of a public attacker. 

### Fees

Because every transaction published into the blockchain imposes on the network the cost of needing to download and verify it, there is a need for some regulatory mechanism, typically involving transaction fees, to prevent abuse. The default approach, used in Bitcoin, is to have purely voluntary fees, relying on miners to act as the gatekeepers and set dynamic minimums. This approach has been received very favorably in the Bitcoin community particularly because it is "market-based", allowing supply and demand between miners and transaction senders determine the price. The problem with this line of reasoning is, however, that transaction processing is not a market; although it is intuitively attractive to construe transaction processing as a service that the miner is offering to the sender, in reality every transaction that a miner includes will need to be processed by every node in the network, so the vast majority of the cost of transaction processing is borne by third parties and not the miner that is making the decision of whether or not to include it. Hence, tragedy-of-the-commons problems are very likely to occur.

However, as it turns out this flaw in the market-based mechanism, when given a particular inaccurate simplifying assumption, magically cancels itself out. The argument is as follows. Suppose that:

1. A transaction leads to `k` operations, offering the reward `kR` to any miner that includes it where `R` is set by the sender and `k` and `R` are (roughly) visible to the miner beforehand.
2. An operation has a processing cost of `C` to any node (ie. all nodes have equal efficiency)
3. There are `N` mining nodes, each with exactly equal processing power (ie. `1/N` of total)
4. No non-mining full nodes exist.

A miner would be willing to process a transaction if the expected reward is greater than the cost. Thus, the expected reward is `kR/N` since the miner has a `1/N` chance of processing the next block, and the processing cost for the miner is simply `kC`. Hence, miners will include transactions where `kR/N > kC`, or `R > NC`. Note that `R` is the per-operation fee provided by the sender, and is thus a lower bound on the benefit that the sender derives from the transaction, and `NC` is the cost to the entire network together of processing an operation. Hence, miners have the incentive to include only those transactions for which the total utilitarian benefit exceeds the cost.

However, there are several important deviations from those assumptions in reality:

1. The miner does pay a higher cost to process the transaction than the other verifying nodes, since the extra verification time delays block propagation and thus increases the chance the block will become a stale.
2. There do exist non-mining full nodes.
3. The mining power distribution may end up radically inegalitarian in practice.
4. Speculators, political enemies and crazies whose utility function includes causing harm to the network do exist, and they can cleverly set up contracts where their cost is much lower than the cost paid by other verifying nodes.

(1) provides a tendency for the miner to include fewer transactions, and (2) increases `NC`; hence, these two effects at least partially cancel each other out.<sup>[Clarification needed](https://github.com/ethereum/wiki/issues/447#issuecomment-316972260)</sup> (3) and (4) are the major issue; to solve them we simply institute a floating cap: no block can have more operations than `BLK_LIMIT_FACTOR` times the long-term exponential moving average. Specifically:

    blk.oplimit = floor((blk.parent.oplimit * (EMA_FACTOR - 1) + floor(parent.opcount * BLK_LIMIT_FACTOR)) / EMA_FACTOR)

`BLK_LIMIT_FACTOR` and `EMA_FACTOR` are constants that will be set to 65536 and 1.5 for the time being, but will likely be changed after further analysis.

There is another factor disincentivizing large block sizes in Bitcoin: blocks that are large will take longer to propagate, and thus have a higher probability of becoming stales. In Ethereum, highly gas-consuming blocks can also take longer to propagate both because they are physically larger and because they take longer to process the transaction state transitions to validate. This delay disincentive is a significant consideration in Bitcoin, but less so in Ethereum because of the GHOST protocol; hence, relying on regulated block limits provides a more stable baseline.

### Computation And Turing-Completeness

An important note is that the Ethereum virtual machine is Turing-complete; this means that EVM code can encode any computation that can be conceivably carried out, including infinite loops. EVM code allows looping in two ways. First, there is a `JUMP` instruction that allows the program to jump back to a previous spot in the code, and a `JUMPI` instruction to do conditional jumping, allowing for statements like `while x < 27: x = x * 2`. Second, contracts can call other contracts, potentially allowing for looping through recursion. This naturally leads to a problem: can malicious users essentially shut miners and full nodes down by forcing them to enter into an infinite loop? The issue arises because of a problem in computer science known as the halting problem: there is no way to tell, in the general case, whether or not a given program will ever halt.

As described in the state transition section, our solution works by requiring a transaction to set a maximum number of computational steps that it is allowed to take, and if execution takes longer computation is reverted but fees are still paid. Messages work in the same way. To show the motivation behind our solution, consider the following examples:

* An attacker creates a contract which runs an infinite loop, and then sends a transaction activating that loop to the miner. The miner will process the transaction, running the infinite loop, and wait for it to run out of gas. Even though the execution runs out of gas and stops halfway through, the transaction is still valid and the miner still claims the fee from the attacker for each computational step.
* An attacker creates a very long infinite loop with the intent of forcing the miner to keep computing for such a long time that by the time computation finishes a few more blocks will have come out and it will not be possible for the miner to include the transaction to claim the fee. However, the attacker will be required to submit a value for `STARTGAS` limiting the number of computational steps that execution can take, so the miner will know ahead of time that the computation will take an excessively large number of steps.
* An attacker sees a contract with code of some form like `send(A,contract.storage[A]); contract.storage[A] = 0`, and sends a transaction with just enough gas to run the first step but not the second (ie. making a withdrawal but not letting the balance go down). The contract author does not need to worry about protecting against such attacks, because if execution stops halfway through the changes get reverted.
* A financial contract works by taking the median of nine proprietary data feeds in order to minimize risk. An attacker takes over one of the data feeds, which is designed to be modifiable via the variable-address-call mechanism described in the section on DAOs, and converts it to run an infinite loop, thereby attempting to force any attempts to claim funds from the financial contract to run out of gas. However, the financial contract can set a gas limit on the message to prevent this problem.

The alternative to Turing-completeness is Turing-incompleteness, where `JUMP` and `JUMPI` do not exist and only one copy of each contract is allowed to exist in the call stack at any given time. With this system, the fee system described and the uncertainties around the effectiveness of our solution might not be necessary, as the cost of executing a contract would be bounded above by its size. Additionally, Turing-incompleteness is not even that big a limitation; out of all the contract examples we have conceived internally, so far only one required a loop, and even that loop could be removed by making 26 repetitions of a one-line piece of code. Given the serious implications of Turing-completeness, and the limited benefit, why not simply have a Turing-incomplete language? In reality, however, Turing-incompleteness is far from a neat solution to the problem. To see why, consider the following contracts:

    C0: call(C1); call(C1);
    C1: call(C2); call(C2);
    C2: call(C3); call(C3);
    ...
    C49: call(C50); call(C50);
    C50: (run one step of a program and record the change in storage)

Now, send a transaction to A. Thus, in 51 transactions, we have a contract that takes up 2<sup>50</sup> computational steps. Miners could try to detect such logic bombs ahead of time by maintaining a value alongside each contract specifying the maximum number of computational steps that it can take, and calculating this for contracts calling other contracts recursively, but that would require miners to forbid contracts that create other contracts (since the creation and execution of all 26 contracts above could easily be rolled into a single contract). Another problematic point is that the address field of a message is a variable, so in general it may not even be possible to tell which other contracts a given contract will call ahead of time. Hence, all in all, we have a surprising conclusion: Turing-completeness is surprisingly easy to manage, and the lack of Turing-completeness is equally surprisingly difficult to manage unless the exact same controls are in place - but in that case why not just let the protocol be Turing-complete?

### Currency And Issuance

The Ethereum network includes its own built-in currency, ether, which serves the dual purpose of providing a primary liquidity layer to allow for efficient exchange between various types of digital assets and, more importantly, of providing a mechanism for paying transaction fees. For convenience and to avoid future argument (see the current mBTC/uBTC/satoshi debate in Bitcoin), the denominations will be pre-labeled:

* 1: wei
* 10<sup>12</sup>: szabo
* 10<sup>15</sup>: finney
* 10<sup>18</sup>: ether

This should be taken as an expanded version of the concept of "dollars" and "cents" or "BTC" and "satoshi". In the near future, we expect "ether" to be used for ordinary transactions, "finney" for microtransactions and "szabo" and "wei" for technical discussions around fees and protocol implementation; the remaining denominations may become useful later and should not be included in clients at this point.

The issuance model will be as follows:

* Ether will be released in a currency sale at the price of 1000-2000 ether per BTC, a mechanism intended to fund the Ethereum organization and pay for development that has been used with success by other platforms such as Mastercoin and NXT. Earlier buyers will benefit from larger discounts. The BTC received from the sale will be used entirely to pay salaries and bounties to developers and invested into various for-profit and non-profit projects in the Ethereum and cryptocurrency ecosystem.
* 0.099x the total amount sold (60102216 ETH) will be allocated to the organization to compensate early contributors and pay ETH-denominated expenses before the genesis block.
* 0.099x the total amount sold will be maintained as a long-term reserve.
* 0.26x the total amount sold will be allocated to miners per year forever after that point.

| Group  | At launch | After 1 year | After 5 years
| ------------- | ------------- |-------------| ----------- |
| Currency units  | 1.198X | 1.458X  |  2.498X |
| Purchasers  | 83.5% | 68.6%  | 40.0% |
| Reserve spent pre-sale | 8.26% | 6.79% | 3.96% |
| Reserve used post-sale | 8.26% | 6.79% | 3.96% |
| Miners | 0% | 17.8% | 52.0% |

**Long-Term Supply Growth Rate (percent)**

![SPV in bitcoin](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/inflation.png)

_Despite the linear currency issuance, just like with Bitcoin over time the supply growth rate nevertheless tends to zero_

The two main choices in the above model are (1) the existence and size of an endowment pool, and (2) the existence of a permanently growing linear supply, as opposed to a capped supply as in Bitcoin. The justification of the endowment pool is as follows. If the endowment pool did not exist, and the linear issuance reduced to 0.217x to provide the same inflation rate, then the total quantity of ether would be 16.5% less and so each unit would be 19.8% more valuable. Hence, in the equilibrium 19.8% more ether would be purchased in the sale, so each unit would once again be exactly as valuable as before. The organization would also then have 1.198x as much BTC, which can be considered to be split into two slices: the original BTC, and the additional 0.198x. Hence, this situation is _exactly equivalent_ to the endowment, but with one important difference: the organization holds purely BTC, and so is not incentivized to support the value of the ether unit.

The permanent linear supply growth model reduces the risk of what some see as excessive wealth concentration in Bitcoin, and gives individuals living in present and future eras a fair chance to acquire currency units, while at the same time retaining a strong incentive to obtain and hold ether because the "supply growth rate" as a percentage still tends to zero over time. We also theorize that because coins are always lost over time due to carelessness, death, etc, and coin loss can be modeled as a percentage of the total supply per year, that the total currency supply in circulation will in fact eventually stabilize at a value equal to the annual issuance divided by the loss rate (eg. at a loss rate of 1%, once the supply reaches 26X then 0.26X will be mined and 0.26X lost every year, creating an equilibrium).

Note that in the future, it is likely that Ethereum will switch to a proof-of-stake model for security, reducing the issuance requirement to somewhere between zero and 0.05X per year. In the event that the Ethereum organization loses funding or for any other reason disappears, we leave open a "social contract": anyone has the right to create a future candidate version of Ethereum, with the only condition being that the quantity of ether must be at most equal to `60102216 * (1.198 + 0.26 * n)` where `n` is the number of years after the genesis block. Creators are free to crowd-sell or otherwise assign some or all of the difference between the PoS-driven supply expansion and the maximum allowable supply expansion to pay for development. Candidate upgrades that do not comply with the social contract may justifiably be forked into compliant versions.

### Mining Centralization

The Bitcoin mining algorithm works by having miners compute SHA256 on slightly modified versions of the block header millions of times over and over again, until eventually one node comes up with a version whose hash is less than the target (currently around 2<sup>192</sup>). However, this mining algorithm is vulnerable to two forms of centralization. First, the mining ecosystem has come to be dominated by ASICs (application-specific integrated circuits), computer chips designed for, and therefore thousands of times more efficient at, the specific task of Bitcoin mining. This means that Bitcoin mining is no longer a highly decentralized and egalitarian pursuit, requiring millions of dollars of capital to effectively participate in. Second, most Bitcoin miners do not actually perform block validation locally; instead, they rely on a centralized mining pool to provide the block headers. This problem is arguably worse: as of the time of this writing, the top three mining pools indirectly control roughly 50% of processing power in the Bitcoin network, although this is mitigated by the fact that miners can switch to other mining pools if a pool or coalition attempts a 51% attack.

The current intent at Ethereum is to use a mining algorithm where miners are required to fetch random data from the state, compute some randomly selected transactions from the last N blocks in the blockchain, and return the hash of the result. This has two important benefits. First, Ethereum contracts can include any kind of computation, so an Ethereum ASIC would essentially be an ASIC for general computation - ie. a better CPU. Second, mining requires access to the entire blockchain, forcing miners to store the entire blockchain and at least be capable of verifying every transaction. This removes the need for centralized mining pools; although mining pools can still serve the legitimate role of evening out the randomness of reward distribution, this function can be served equally well by peer-to-peer pools with no central control.

This model is untested, and there may be difficulties along the way in avoiding certain clever optimizations when using contract execution as a mining algorithm. However, one notably interesting feature of this algorithm is that it allows anyone to "poison the well", by introducing a large number of contracts into the blockchain specifically designed to stymie certain ASICs. The economic incentives exist for ASIC manufacturers to use such a trick to attack each other. Thus, the solution that we are developing is ultimately an adaptive economic human solution rather than purely a technical one.

### Scalability

One common concern about Ethereum is the issue of scalability. Like Bitcoin, Ethereum suffers from the flaw that every transaction needs to be processed by every node in the network. With Bitcoin, the size of the current blockchain rests at about 15 GB, growing by about 1 MB per hour. If the Bitcoin network were to process Visa's 2000 transactions per second, it would grow by 1 MB per three seconds (1 GB per hour, 8 TB per year). Ethereum is likely to suffer a similar growth pattern, worsened by the fact that there will be many applications on top of the Ethereum blockchain instead of just a currency as is the case with Bitcoin, but ameliorated by the fact that Ethereum full nodes need to store just the state instead of the entire blockchain history.

The problem with such a large blockchain size is centralization risk. If the blockchain size increases to, say, 100 TB, then the likely scenario would be that only a very small number of large businesses would run full nodes, with all regular users using light SPV nodes. In such a situation, there arises the potential concern that the full nodes could band together and all agree to cheat in some profitable fashion (eg. change the block reward, give themselves BTC). Light nodes would have no way of detecting this immediately. Of course, at least one honest full node would likely exist, and after a few hours information about the fraud would trickle out through channels like Reddit, but at that point it would be too late: it would be up to the ordinary users to organize an effort to blacklist the given blocks, a massive and likely infeasible coordination problem on a similar scale as that of pulling off a successful 51% attack. In the case of Bitcoin, this is currently a problem, but there exists a blockchain modification [suggested by Peter Todd](https://web.archive.org/web/20140623061815/http://sourceforge.net/p/bitcoin/mailman/message/31709140/) which will alleviate this issue.

In the near term, Ethereum will use two additional strategies to cope with this problem. First, because of the blockchain-based mining algorithms, at least every miner will be forced to be a full node, creating a lower bound on the number of full nodes. Second and more importantly, however, we will include an intermediate state tree root in the blockchain after processing each transaction. Even if block validation is centralized, as long as one honest verifying node exists, the centralization problem can be circumvented via a verification protocol. If a miner publishes an invalid block, that block must either be badly formatted, or the state `S[n]` is incorrect. Since `S[0]` is known to be correct, there must be some first state `S[i]` that is incorrect where `S[i-1]` is correct. The verifying node would provide the index `i`, along with a "proof of invalidity" consisting of the subset of Patricia tree nodes needing to process `APPLY(S[i-1],TX[i]) -> S[i]`. Nodes would be able to use those nodes to run that part of the computation, and see that the `S[i]` generated does not match the `S[i]` provided.

Another, more sophisticated, attack would involve the malicious miners publishing incomplete blocks, so the full information does not even exist to determine whether or not blocks are valid. The solution to this is a challenge-response protocol: verification nodes issue "challenges" in the form of target transaction indices, and upon receiving a node a light node treats the block as untrusted until another node, whether the miner or another verifier, provides a subset of Patricia nodes as a proof of validity.

## Conclusion

The Ethereum protocol was originally conceived as an upgraded version of a cryptocurrency, providing advanced features such as on-blockchain escrow, withdrawal limits, financial contracts, gambling markets and the like via a highly generalized programming language. The Ethereum protocol would not "support" any of the applications directly, but the existence of a Turing-complete programming language means that arbitrary contracts can theoretically be created for any transaction type or application. What is more interesting about Ethereum, however, is that the Ethereum protocol moves far beyond just currency. Protocols around decentralized file storage, decentralized computation and decentralized prediction markets, among dozens of other such concepts, have the potential to substantially increase the efficiency of the computational industry, and provide a massive boost to other peer-to-peer protocols by adding for the first time an economic layer. Finally, there is also a substantial array of applications that have nothing to do with money at all.

The concept of an arbitrary state transition function as implemented by the Ethereum protocol provides for a platform with unique potential; rather than being a closed-ended, single-purpose protocol intended for a specific array of applications in data storage, gambling or finance, Ethereum is open-ended by design, and we believe that it is extremely well-suited to serving as a foundational layer for a very large number of both financial and non-financial protocols in the years to come.

## Notes, References and Further Reading

#### Notes

1. A sophisticated reader may notice that in fact a Bitcoin address is the hash of the elliptic curve public key[<sup>[15]</sup>](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm), and not the public key itself. However, it is in fact perfectly legitimate cryptographic terminology to refer to the pubkey hash as a public key itself. This is because Bitcoin's cryptography can be considered to be a custom digital signature algorithm, where the public key consists of the hash of the ECC pubkey; the signature consists of the ECC pubkey concatenated with the ECC signature; and the verification algorithm involves checking the ECC pubkey in the signature against the ECC pubkey hash provided as a public key, and then verifying the ECC signature against the ECC pubkey.
2. Technically, the median of the 11 previous blocks.
3. An oracle contract needs external information not present in the contract itself nor the blockchain. Converting USD to BTC would require an oracle, external data in a contract which in this case is used for an accurate conversion to occur at a given time—which would be be a dynamic external exchange rate between BTC and USD.
4. Internally, 2 and "CHARLIE" are both numbers, with the latter being in big-endian base 256 representation. Numbers can be at least 0 and at most 2<sup>256</sup>-1.
5. Note made on 7 July 2017 for those not familiar with arrays in programming languages: the index starts at 0, and increments by 1. So the nth item in an array has an index of n-1. So in the case in question, the item in the list with an index of 2, is the 3rd item, which has a value of 0. (To learn a programming language it is often suggested to learn Python first as it is designed to be easy to learn, read and understand. [Learn Python the Hard Way](https://www.learnpythonthehardway.org/) is often touted as a good way for learning Python. There are many free, online platforms available (as well as paid online platforms and in person training such as university courses) for learning programming languages, including [Codecademy](https://www.codecademy.com/) (which provides an introduction to many programming languages), learncpp.com (which provides in-depth training of C++). C++ is a good language to learn for Ethereum's Solidity programming language, which is used to build decentralized apps.

#### References
1a. Nakamoto, S. 31 October 2008. "Bitcoin: A Peer-to-Peer Electronic Cash System". Also known as the Bitcoin whitepaper. http://nakamotoinstitute.org/bitcoin/. http://bitcoin.org/bitcoin.pdf. https://github.com/saivann/bitcoinwhitepaper. Accessed 7 July 2017.  
1b. Davis, J. 10 October 2011. "The Crypto-Currency: Bitcoin and its mysterious inventor". The New Yorker. <http://www.newyorker.com/magazine/2011/10/10/the-crypto-currency>. Retrieved 31 October 2014. Accessed 7 July 2017.
2. Unknown author. Unknown date. "Intrinsic value". http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/. Unable to access 7 July 2017.  
3. Assia, Y.; Vitalik, B.; Haki, M.; Meni R.; and Rotem, L. U.d. "Colored coins whitepaper".   https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit. Accessed 7 July 2017.
4. Last edited on 10 May 2016. "Smart property". Bitcoin Wiki. https://en.bitcoin.it/wiki/Smart_Property. Accessed 7 July 2017.  
5. Last modified 6 July 2017. "Namecoin". https://namecoin.org/. Accessed 7 July 2017.  
6. Last edited on 24 June 2017. "Smart contracts". Bitcoin Wiki. https://en.bitcoin.it/wiki/Contracts. Accessed 7 July 2017.  
7. Buterin, V. Sep 19, 2013. "Bootstrapping A Decentralized Autonomous Corporation: Part I". Bitcoin Magazine. http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i. Accessed 7 July 2017.    
8. "Blind signature". Last modified 29 March 2017. Wikipedia. https://en.wikipedia.org/wiki/Blind_signature. Accessed 7 July 2017.  
9. Dai, W. U.d. "B-money". http://www.weidai.com/bmoney.txt. Accessed 7 July 2017.    
10. Hal, F. Reusable proofs of work: http://www.finney.org/~hal/rpow/. Unable to access 7 July 2017.  
11. Back, A. U.d. Hashcash. http://www.hashcash.org/. Accessed 7 July 2017.  
12. Last edited on 15 June 2017. "Sybil attack". Wikipedia. https://en.wikipedia.org/wiki/Sybil_attack. Accessed 7 July 2017.  
13. Last edited on 30 June 2017. "SHA-2". Wikipedia.https://en.wikipedia.org/wiki/SHA-2.  Accessed 7 July 2017.
14. Szabo, N. 1998. "Secure property titles with owner authority". http://szabo.best.vwh.net/securetitle.html. Unable to access 7 July 2017. Alternative link here: http://nakamotoinstitute.org/secure-property-titles/. Accessed 7 July 2017.
15. Last edited on 27 June 2017. "Elliptic Curve Digital Signature Algorithm". Wikipedia.  https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm. Accessed 7 July 2017.  
16. Dogecoin. http://dogecoin.com/. Accessed 7 July 2017.  
17. Last edited on 29 June 2017. "Denial-of-service attack". Wikipedia. https://en.wikipedia.org/wiki/Denial-of-service_attack. Accessed 7 July 2017.

#### Further reading:
1. Zooko's triangle: http://en.wikipedia.org/wiki/Zooko's_triangle  
2. Mastercoin whitepaper: https://github.com/mastercoin-MSC/spec  
3. Simplified payment verification: https://en.bitcoin.it/wiki/Scalability#Simplifiedpaymentverification  
4. Merkle trees: http://en.wikipedia.org/wiki/Merkle_tree  
5. Patricia trees: http://en.wikipedia.org/wiki/Patricia_tree   
6. GHOST: http://www.cs.huji.ac.il/~yoni_sompo/pubs/15/btc_scalability_full.pdf  
7. StorJ and Autonomous Agents, Jeff Garzik: http://garzikrants.blogspot.ca/2013/01/storj-and-bitcoin-autonomous-agents.html  
8. Mike Hearn on Smart Property at Turing Festival: http://www.youtube.com/watch?v=Pu4PAMFPo5Y  
9. Ethereum RLP: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP  
10. Ethereum Merkle Patricia trees: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree  
11. Peter Todd on Merkle sum trees: http://sourceforge.net/p/bitcoin/mailman/message/31709140/  