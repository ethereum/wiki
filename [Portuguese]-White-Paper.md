# Em progresso...

###Uma Plataforma para Smart Contracts e Aplicações Decentralizadas da Próxima Geração

O desenvolvimento do Bitcoin por Satoshi Nakamoto em 2009 tem frequentemente sido aclamado como uma revolução do dinheiro e da moeda, sendo o primeiro exemplo de ativo digital que simultaneamente não possui nenhum lastro ou "[valor intrínseco](http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/)" e nenhum emissor/controlador centralizado. Entretanto, outra -- provavelmente mais importante -- parte do experimento do Bitcoin é a tecnologia do blockchain -- o "motor" da criptomoeda -- como uma ferramenta de consenso distribuído, e a atenção está rapidamente começando a mudar em direção a esse outro aspecto do Bitcoin. Dentre as aplicações alternativas da tecnologia do blockchain, normalmente incluem-se a utilização de ativos digitas no blockchain para representar moedas personalizadas e instrumentos financeiros (["colored coins"](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit)), a propriedade de um dispositivo físico subjacente (["smart property"](https://en.bitcoin.it/wiki/Smart_Property)), ativos não-fungíveis, como domínios na internet (["Namecoin"](http://namecoin.org)), tal como aplicações mais complexas envolvendo ter ativos digitais sendo controlados diretamente por um pedaço de código implementando regras arbitrárias (["smart contracts"](http://szabo.best.vwh.net/smart_contracts_idea.html)) ou até mesmo "[organizações autônomas descentralizadas](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/)" (Em inglês, _Decentralized Autonomous Organizations_ -- DAOs) baseadas no blockchain. O que o Ethereum pretende prover é um blockchain com uma linguagem de programação verdadeiramente Turing-completa, que pode ser utilizada para criar "contratos" que podem ser usados para codificar funções arbitrárias de transição de estado, permitindo que os usuários criem qualquer um dos sistemas descritos acima, assim como muitos outros que nós se quer ainda imaginamos, simplesmente escrevendo sua lógica em poucas linhas de código.

### Sumário

* [História](#historia)
    * [Bitcion como um Sistema de Transição de Estados](#bitcoin-como-um-sistema-de-transicao-de-estados)
    * [Mineração](#mineracao)
    * [_Merkle Trees_](#merkle-trees)
    * [Aplicações Alternativas do Blockchain](#aplicacoes-alternativas-do-blockchain)
    * [_Scripting_](#scripting)
* [Ethereum](#ethereum)
    * [Contas no Ethereum](#contas-no-ethereum)
    * [Mensagens e Transações](#mensagens-e-transacoes)
    * [Função de Transição de Estado no Ethereum](#funcao-de-transacao-de-estado-no-ethereum)
    * [Execução do Código](#execucao-do-codigo)
    * [Blockchain e Mineração](#blockchain-e-mineracao)
* [Aplicações](#aplicacoes)
    * [Sistemas de Token](#sistemas-de-token)
    * [Derivativos Financeiros](#derivativos-financeiros)
    * [Sistemas de Identidade e Reputação](#sistemas-de-identidade-e-reputacao)
    * [Armazenamento Descentralizado de Arquivos](#armazenamento-descentralizado-de-arquivos)
    * [Organizações Autônomas Descentralizadas (DAOs)](#organizacoes-autonomas-descentralizadas)
    * [Mais Aplicações](#mais-aplicacoes)
* [Miscelânea e Preocupações](#miscelanea-e-preocupações)
    * [Implementação modificada do GHOST](#implementacao-modificada-do-ghost)
    * [Taxas](#taxas)
    * [Computação e Turing-completude](#computacao-e-turing-completude)
    * [Moeda e Emissão](#moeda-e-emissao)
    * [Centralização da Mineração](#centralizacao-da-mineracao)
    * [Escalabilidade](#escalabilidade)
* [Conclusão](#conclusao)
* [Notas e Leituras Posteriores](#notas-e-leituras-posteriores)

## Introdução ao Bitcoin e conceitos existentes

### História

O conceito de moeda digital descentralizada, assim como as aplicações alternativas como registro de propriedade, já existem há decadas. Os protocolos anônimos de _e-cash_ dos anos 1980 e 1990 eram basicamente dependentes de uma primitiva criptográfica conhecida como _Chaumian Blinding_. Chaumian Blinding provia essas novas moedas com alto grau de privacidade, mas seus protocolos falhavam miseravelmente em ganhar força por causa da sua dependência de um intermediário centralizado. Em 1998, Wei Dai criou o [b-money](http://www.weidai.com/bmoney.txt), que se tornou a primeira proposta de se introduzir a ideia de criar dinheiro através da resolução de enigmas computacionais e também como consenso descentralizado, mas a proposta possuia poucos detalhes sobre como o consenso descentralizado iria ser implementado de fato. Em 2005, Hal Finney introduziu o conceito de "[provas de trabalho reutilizáveis (em inglês, _reusable proof-of-work_)](http://www.finney.org/~hal/rpow/)", um sistema que usa ideias do b-money junto com os computacionalmente custosos enigmas Hashcash -- criados por Adam Back -- para criar um conceito para uma criptomoeda, entretanto, mais uma vez ficou aquém do ideal por depender de computação confiável como _backend_. Em 2009, uma moeda descentralizada foi implementada pela primeira vez na prática por Satoshi Nakamoto, combinando as conhecidas primitivas para gestão da propriedade através de criptografia de chave pública (_public key cryptography_) com um algoritmo de consenso para manter o controle de quem possui moedas, conhecido como "prova de trabalho" (em inglês, _proof of work_).

O mecanismo por trás do _proof of work_ foi uma revolução porque ele resolveu dois problemas simultaneamente. Primeiro, ele provia um algoritmo de consenso simples e razoavelmente efetivo, permitindo que nós em uma rede coletivamente concordassem sobre um conjunto de atualizações no estado do livro-contábil do Bicoin. Segundo, ele provia um mecanismo para permitir a livre entrada no processo de consenso, resolvendo o problema político de decidir quem pode influenciar no consenso, enquanto simultaneamente previne [_Sybil attacks_](https://en.wikipedia.org/wiki/Sybil_attack). O _proof of work_ faz isso substituindo uma barreira formal à participação, como o requerimento de ser registrado como uma entidade única em uma lista particular, com uma barreira econômica: o peso de um único nó no processo de votação por consenso é diretamente proporcional ao poder computacional que o mesmo possui. Desde então, uma abordagem alternativa tem sido proposta -- a chamada "prova de participação" (em inglês, _proof of stake_), calculando o peso de um nó como sendo proporcional as suas posses na criptomoeda, e não a seus recursos computacionais. A discussão a respeito dos méritos relativos das duas abordagens está além do escopo deste paper, mas é importante notar que ambas podem ser usadas para servir como _backbone_ de uma criptomoeda.

### Bitcion como um Sistema de Transição de Estados

![statetransition.png](http://vitalik.ca/files/statetransition.png?2)

Do ponto de vista técnico, o livro-contábil de uma criptomoeda como o Bitcoin pode ser imaginado como um sistema de transição de estados, onde existe um "estado" consistindo no status de propriedade de todos os bitcoins existentes e uma "função de transição de estado", que recebe um estado e uma transação e gera um novo estado como resultado. Em um sistema bancário tradicional, por exmemplo, o estado é o balancete, uma trançaão é uma requisição de mover `$X` de `A` para `B`, e a função de transição de estados reduz o valor da conta de `A` em `$X` e aumenta o valor da conta de `B` em `$X`. Se a conta de `A` tem menos que `$X` de saldo, a função de transição retorna um erro. Assim, pode-se definir formalmente:

    APPLY(S,TX) -> S' or ERROR

No sistema bancário definido acima:

    APPLY({ Alice: $50, Bob: $50 },"Envie $20 de Alice para Bob") = { Alice: $30, Bob: $70 }

Mas:
    
    APPLY({ Alice: $50, Bob: $50 },"Envie $70 de Alice para Bob") = ERROR

O "estado" no Bitcoin é o conjunto de todas as moedas (tecnicamente, "saídas não-gastas de transações" -- em inglês, _unspent transaction outputs_ ou UTXO) que foram mineradas, mas ainda não gastas, com cada UTXO tendo uma denominação e um dono (definido por um endereço de 20-bytes que é essencialmente uma chave criptográfica pública<sup>[1]</sup>. Uma transação contém uma ou mais entradas, com cada entrada contendo uma referência para um UTXO existente e uma assinatura criptográfica produzida pela chave privada associada à chave ao endereço do dono, e uma ou mais saídas, com cada uma contendo um nvo UTXO para ser adicionado no novo estado.

A função de transição de estado `APPLY(S,TX) -> S'` pode ser definida aproximadamente como segue:

1. Para cada input em `TX`:
    * Se o UTXO referenciado não pertence a `S`, retorne um erro.
    * Se a assinatura fornecida não pertence ao dono do UTXO, retorne um erro.
2. Se a soma das denominações de todas as entradas UTXO é menos que a soma das denominações de todas as saídas UTXO, retorne um erro.
3. Retorne `S'` com todas as entradas UTXO removidas e todas as saídas UTXO adicionadas.

A primeira metade do primeiro passo previne que o remetente da transação gaste moedas que não existem; a segunda metade previne que o remetente gaste moedas de outras pessoas. O segundo passo força a conservação de valor. Afim de usar isso tudo para pagamento, o protocolo funciona desta forma: suponha que Alice queira enviar `11,7 BTC` para Bob. Primeiro, Alice irá procurar por um conjunto dos UTXO disponíveis que ela possua e que juntos somem pelo menos `11,7 BTC`. Realisticamente, Alice raramente será capaz de obter exatamente `11,7 BTC`; digamos que a menor quantia que ela consegue juntar é `6 + 4 + 2 = 12`. Ela então cria uma transação com aquelas três entradas e duas saídas. A primeira saída será os `11.7 BTC` com o endereço de Bob como seu dono e a segunda será o "troco" com os `0,3 BTC` restantes. Se Alice não reivindicar o troco para si mesma, enviando-o para um endereço que ela possui, o minerador terá o direito de mantê-lo para si ("fique com o troco").

### Mineração

![block_picture.jpg](http://vitalik.ca/files/block_picture.png)

Se nós tivéssemos acesso a um serviço centralizado confiável, este sistema seria trivial de se implementar; ele poderia ser codificado exatamente como descrito, usando o HD do servidor centralizado para manter o controle do estado da rede. Entretanto, com o Bitcion estamos estamos tentando construir um sistema monetário descentralizado, então precisaremos combinar o sistema de transição de estados com um sistema de consenso de modo a garantir que todos concordem com a ordem das transações. O processo de consenso descentralizado do Bitcoin requer que os nós na rede continuamente tentem produzir pacotes de transações chamados de "blocos". É esperado que a rede crie um novo block válido a cada dez minutos aproximadamente, com cada bloco contendo um _timestamp_, um _nonce_, uma referência para (ex.: um _hash_ do) o bloco anterior e uma lista de todas as transações que ocorreram desde o último bloco. Com o passar do tempo, isso cria um "blockchain" persistente, que cresce indefinidamente e é continuamente atualizado para representar o último estado do livro-contábil do Bitcoin.

O algoritmo para checar que um bloco é válido, expressado nesse paradigma, é como segue:

1. Checar se o bloco anterior referenciado pelo bloco atual existe e é válido.
2. Checar se o _timestamp_  do bloco é maior que o do bloco anterior<sup>[2]</sup> e menos que 2 horas no futuro.
3. Checar se o _proff of work_ do bloco é válido.
4. Deixar que `S[0]` seja o estado ao fim do bloco anterior.
5. Supor que `TX` é a lista de transações do bloco com `n` transações. Para cada `i` em `0...n-1`, faça `S[i+1] = APPLY(S[i],TX[i])`. Se alguma aplicação retornar um erro, saia e retorne falso.
6. Retornar true e registrar `S[n]` como o estado ao final do bloco atual.

Essencialmente, cada transação no bloco precisa prover uma transição de estado válida a partir do estado canônico de antes da transação ser executada para um novo estado. Note que o estado não está codificado no bloco de forma alguma; trata-se puramente de uma abstração para ser lembrada pelo nó validador e pode apenas ser (seguramente) computada para qualquer bloco partindo-se do estado genesis e sequencialmente aplicando cada transição em todos os blocos. Adicionalmente, note que a order na qual o minerador inclui transações no bloco importa; se há duas transações `A` e `B` tal que `B` gasta um UTXO criado por `A`, então o bloco será válido somente se `A` vem antes de `B`, mas não o contrário.

A única condição de validez presente na lista acima que não é encontrada em nenhum outro sistema é o requerimento do _proof of work_ . A exata condição é que o duplo-hash-SHA256 de cada bloco -- tratado como um número de 256-bits -- deve ser menor que um _alvo_ dinamicamente ajustado, o qual no momento da redação deste texto é aproximadamente 2<sup>187</sup>. O propósito disto é fazer com que a criação de um bloco seja computacionalmente difícil, assim prevenindo que _Sybil attackers_ refaçam todo o blockchain favorecendo-se. Como o SHA256 é projetado para ser uma função pseudoaleatória completamente imprevisível, a única forma de criar um bloco válido é simplesmente por tentativa e erro, repetidamente incrementando o _nonce_ e verificando se o novo _hash_ satisfaz a condição de ser megor que o _alvo_.

Com o atual _alvo_ de ~2<sup>187</sup>, a rede precisa tentar, em média, ~2<sup>69</sup> vezes antes de encontrar um bloco válido; em geral, o _alvo_ é recalculado pela rede a cada 2016 blocos, para que, na média, um novo bloco seja produzido por algum nó da rede a cada dez minutos. Para compensar os mineradores por esse trabalho computacional, estes possuem o direito de incluir um tipo especial de transação ("coinbase") em cada bloco, creditando -- no momento da redação deste texto -- a eles mesmos 25 BTC criados a partir do nada. Além disso, se qualquer transação tem uma donominação total maior em suas entradas que em suas saídas, a diferença também passa a ser de direito do minerador, como uma "taxa de transação". Propositalmente, este é também o único mecanismo pelo qual BTC são emitidos; o estado genesis não continha nenhuma moeda.

De modo a melhor compreender o propósito da mineração, vamos examinar o que acontece quando existe um atacante malicioso na rede. Como a criptografia subjacente ao Bitcoin é assumida como segura, alguém mal-intencionado irá atacar a única parte do Bitcoin que não é protegido diretamente por criptografia: a ordem das transações. A estratégia do atacante é simples:

1. Enviar 100 BTC para um comerciante em troca de algum produto (preferivelmente, um bem digital de entrega rápida).
2. Esperar pela entrega do produto.
3. Produzir uma outra transação enviando os mesmos 100 BTC para ele mesmo.
4. Tentar convencer a rede que a transação dele para ele mesmo é a que veio primeiro.

Uma vez que o passo (1) foi realizado, depois de alguns minutos, algum minerador irá incluir a transação em um bloco, digamos o bloco nº 270.000. Depois de aproximadamente uma hora, mais cinco blocos foram adicionados ao blockchain depois daquele bloco, com cada um deles apontando indiretamente para aquela transação e, portanto, "confirmando-a". Neste ponto, o comerciante irá aceitar o pagamento como finalizado e entregar o produto; como estamos assumindo que esta é uma mercadoria digital, a entrega é instantânea. Agora, o atacante cria outra transação enviando 100 BTC para ele mesmo. Se ele simplesmente envia a transação para a rede, ela não será processada; mineradores tentarão rodar `APPLY(S,TX)` e notar que `TX` consome um UTXO que não está mais naquele estado `S`. Então, em vez disso, o atacante cria um _fork_ do blockchain, começando por minerar outra versão do bloco 270.000, apontando para o mesmo bloco 269.999 como pais, mas com a nova transação no lugar da original. Visto que os dados do bloco são diferentes, isto requer refazer o _proof of work_. Além disso, a nova versão do bloco 270.000 tem um _hash_ diferente, então os blocos 270.001 a 270.005 não "apontam" para ele; deste modo, a corrente original e a nova corrente do atacante estão completamente separadas. A regra é que, no caso de um _fork_, a cadeia mais longa do blockchain é assumida como verdadeira e, assim, mineradores honestos irão trabalhar sobre a cadeia com o bloco 270.005, enquanto o atacante estará trabalhando na cadeia com o novo bloco 270.000. A fim de que o atacante consiga fazer seu blockchain o mais longo, ele precisaria ter mais poder computacional que o restante da rede combinada para poder alcançá-la (o que é conhecido como "o ataque dos 51%").

## (CONTINUA...)