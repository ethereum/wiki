---
name: Layers
category: 
---

###Layers

Networking daemon

Scop:networking daemon trebuie sa se poata conecta la alte noduri Ethereum, si sa accepte si sa imparta datele, pentru a servi drept coloana de sprijin pentru reteaua Ethereum. Daemon-ul in sine trebuie sa fie cat mai neutru posibil; ideal ar trebui sa fie util ca parte a oricarei valute sau protocol cryptographic fara modificari. Trebuie sa includa trasaturi anti DDoS, si poate sa mentina un running score care sa previna orice IP singur de a face mai mult de un request pe secunda.
Interfata:
1.	De fiecare data cand Daemon-ul primeste un nou mesaj (i.e. H(M) nu este egal cu H(Mprev) pentru orice Mprev primit anterior), ar trebui sa trimita o cerere POST la http://localhost: containing the data, unde outputport poate fi setat in fisierul ~/.ethereum/ethereum.conf (1242 implicit, in cazul in care nu este setat)
2.	the daemon should listen on port, unde inputport poate fi setat in fisierul   ~/.ethereum/ethereum.conf (1243 implicit, daca nu este setat), si daca primeste un mesaj intr-un post care il solicita ar trebui sa impinga datele spre toate nodurile conectate ale retelei. Dependente: internet, noduri bootstrapping


Ethereum Core

Layer de comunicare (Communication Layer)

1.Process_network_message(sir) -> msg sau tranzactie sau block
2.Create_network_message

IO Layer (ie. De obicei baza de date)

1.	Insert object(value) (key = sha256(value))
2.	Get object (key) -> value
Data layer
1.	get_transaction
2.	get_block
3.	get_address_balance / nonce
4.	get_contract_memory_at_index
5.	get_contract_root
6.	get_object (Merkle trie nodes, etc)
7.	block class
8.	transaction class
Manager
1.	get_latest_block
2.	add_block (including validation and total difficulty calculation)
3.	apply_transactions_to_block (for miners)
Application Layer
1.	Wallet
2.	Full
3.	SPV
4.	Graphical block explorer
5.	Miner
Clase
Clasa Block
Scop: clasa block trebuia sa poata analiza block-uri, sa le serializeze si sa poata manipula anumite operatiuni de actualizare la block-uri.
Interfata:
Interface:
1.	deserialize: string -> void (constructor)
2.	get_balance: number address -> number
3.	get_contract_state: number address, number index -> number
4.	update_balance: number address, number newbalance -> void
5.	update_contract_state: number address, number index, number newvalue -> void
6.	get_contract_size: number address -> number
7.	serialize: void -> string
Clasa Tranzactii
Scop: clasa tranzactii trebuie sa poata analiza tranzactiile, sa le semneze si sa le serializeze. Retineti ca metoda de serializare poate esua pentru tranzactii fara semnatura, tranzactiile care sunt create intr-un block si nu sunt deserializate nu ar trebui sa fie, de fapt, serializate sau stocate, in nici o forma.
Interfata:
1.	deserialize: string -> void (constructor)
2.	sign: privkey -> void
3.	serialize: void -> string
4.	create: number address, number value, number fee, array[number] data -> void (constructor)
5.	hash: void -> number
6.	to, from, value, fee, data (accessible and settable member variables)
