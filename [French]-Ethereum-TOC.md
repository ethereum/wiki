# Contrats Intelligents et Plateforme d'Application Décentralisée de nouvelle génération

Lorsque Satoshi Nakamoto lance Bitcoin le premier réseaux décentralisé cryptographié (Blockchain) en Janvier 2009, il introduit simultanément deux concepts radicaux et très incertains. Le premier est le " bitcoin " , une monnaie décentralisée peer-to-peer qui conserve une valeur sans support financier d'un emetteur centralisé, ni valeur intrinsèque. Jusqu'à présent, le "bitcoin" comme monnaie a retenu l'essentiel de l'attention du public. D'une part en termes d'aspects politiques d'une monnaie sans banque centrale et d'autre part, à cause de son extrême volatilité à la hausse et à la baisse des prix. Le second concept, tout aussi important, qui emerge de l'expérience de Satoshi : un accord public sur l'ordre des transactions sous forme de preuve de travail, le Blockchain. Bitcoin peut être décrit comme un système du «premier à déposer son action» : si une entité dispose 50 BTC et envoie simultanément à A et à B la meme somme 50 BTC, seule l'opération qui sera validée en premiere sera confirmée. En partant de deux opérations, il n'existe pas de methode pour déterminer laquelle est venue en premiere, et pendant des décennies ce manque a entravé le développement d'une monnaie numérique décentralisée . Le blockchain de Satoshi était la première solution décentralisée crédible. Désormais, l'attention commence rapidement à se déplacer vers cette deuxième partie de la technologie, et vers l'utilisation du concept de blockchain pour autre chose que de l'argent.

Les applications les plus communément citées utilisent un avoir numérique sur le blockchain pour représenter des monnaies et instruments financiers sur-mesure ("colored coins"),  la proprieté d'un actif sous-jacent ("smart property"), des actifs non-fongible comme les noms de domaine ("Namecoin") ainsi que des applications plus développées comme une plateforme de bourse décentralisée, de dérivés financiers, de jeux en ligne P2P, d'identité ou de reputation. Un autre domaine important de recherche : les systèmes de "smart contracts" qui transfèrent des actifs numériques selon des règles préétablies. Par exemple, un contract de liquidité pourrait avoir la forme : "A peut retirer un montant X de ses fonds par jours, B peut retirer Y par jours, ensemble A et B peuvent retirer ce qu'ils désirent, enfin A peut restreindre l'habilité de B d'avoir accès à ses fonds. L'extension logique de ce qui précède est l'Organisation Autonome Décentralisée (OAD) - Des contracts intelligents qui contiennent les actifs et les statuts de l'organisation tout entière. Ethereum aspire à fournir un Blockchain fondé sur un language de programmation Turing-complet à part entière qui peut être utilisé pour créer des contracts qui encodent des fonctions d'états de transition arbitraire, permettant à l'utilisateur de créer les systèmes décris précédemment, ainsi que de nombreuses applications que nous n'avons pas encore imaginées, en rédigeant une suite logique en quelques lignes de code.



**Table des matières :**

1. Histoire
1.1. Bitcoin comme système d'états transitoires
1.2. Mining
1.3. Arbre de Merkle
1.4. Application alternative sur le blockchain
1.5. Scripting


2. Ethereum
2.1 Compte Ethereum
2.2 Messages and Transactions
2.3 Fonction de Transition d'Etat Ethereum 
2.4 Code d'Execution
2.5 Blockchain et Mining

3. Applications
3.1 Monnaies
3.2 Dérivés Financiers
3.3 Système d'Identité et de Reputation
3.4 Stockage de Fichiers Décentralisé
3.5 Organisation Autonome décentralisée
3.6 Nouvelles applications

4. Miscellanées et préoccupations
4.1 Mise en oeuvre GHOST modifiée
4.2 Commissions
4.3 Calculs et Turing-Complet
4.4 Monnaie et émission de monnaie
4.5 Centralisation du minage
4.6 Extensibilité du modèle

5. Conclusion
5.1 References et suggestions de lecture



**Histoire**

Le concept d'une monnaie digitale décentralisée, ainsi que les applications alternatives comme l'enregistrement de propriétés, existe depuis des décennies. Les protocols anonymes de monnaie éléctronique des années 1980 et 1990, principalement dépendant de cryptographie primitive connue sous le nom de signature aveugle de Chaum (Chaumian blinding), proposaient une monnaie avec un haut degré de confidentialité. Cependant, le protocole reposait sur un intermediaire centralisé, ce qui freina son adoption. En 1998, Wei Dai introduit b-money, la premiere proposition à introduire la création de monnaie par la résolution de problemes informatiques et un consensus décentralisé. En 2005, Hal Finney introduit le concept de preuves de travail réutilisables "reusable proofs of work", mêlant les travaux de recherche sur b-money à la résolution de la preuve de Hashcash pour créer le concept de cryptomonnaie. Encore une fois le système reposait sur un tiers.

Parce qu'une monnaie repose sur le système du premier-déposant, où l'ordre de transaction est souvent d'une importance critique, une monnaie décentralisée requiert une solution provenant d'un consensus décentralisé. Les pré-Bitcoins ont dû faire face à l'obstacle de ne résoudre que la moitié du problème en se concentrant, pendant plusieurs années, sur la création d'un consensus multi-partie tolerant les pannes Byzantines. Les protocoles supposaient que tous les participants du système étaient connus et généraient des marges de sécurité de la forme : "S'il y a N Participants, Alors le système peut tolérer N/4 participants malicieux". Toutefois, dans un tel système, les marges de sécurité sont vulnérables aux attaques Sybil, où un individu peut créer des milliers de noeuds simulés sur un serveur ou un botnet et les utiliser pour sécuriser une part majoritaire.

Satoshi innova en proposant l'association d'un protocol de consensus décentralisé très simple, basé sur des noeuds combinant les transactions dans un 'bloc" toutes les dix minutes créant une chaine de blocs ("blockchain") en expansion permanente, à la preuve de travail ("proof of work") comme mechanisme par lequel les noeuds gagnent le droit de participer au système. Tandis que les noeuds disposant d'une grande capacité de calcul ont une influence proportionnelle, il est plus facile de créer un million de noeuds que de concevoir une puissance de calcul supérieure à la moitié du réseaux. Malgré sa simplicité et sa crudité, le modèle de chaine de bloc du Bitcoin a su s'imposer, et pourrait être le berceau dans les cinq prochaines années de plusieurs centaines de monnaies et de protocols à travers le monde.


**Bitcoin comme système d'états transitoires**

From a technical standpoint, the Bitcoin ledger can be thought of as a state transition system, where there is a "state" consisting of the ownership status of all existing bitcoins and a "state transition function" that takes a state and a transaction and outputs a new state which is the result. In a standard banking system, for example, the state is a balance sheet, a transaction is a request to move $X from A to B, and the state transition function reduces the value in A's account by $X and increases the value in B's account by $X. If A's account has less than $X in the first place, the state transition function returns an error. Hence, one can formally define:

D'un point de vue technique, le "grand livre" du Bitcoin peut être perçu comme un système d'états transitoires. 


Signature Aveugle de Chaum :
http://www.hit.bme.hu/~buttyan/courses/BMEVIHIM219/2009/Chaum.BlindSigForPayment.1982.PDF
Hashcash :
http://fr.wikipedia.org/wiki/Hashcash
Attaque Sybil :
http://en.wikipedia.org/wiki/Sybil_attack