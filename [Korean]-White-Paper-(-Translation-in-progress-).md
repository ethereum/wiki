### 차세대 스마트 컨트랙트와 분산화된 응용 프로그램 플랫폼

Satoshi Nakamoto가 2009년 1월 비트코인 블록체인을 처음 세상에 내보냈을 때, 혁신적이고 시험되지 않은 두 가지 개념을 동시에 제시했다. 첫번째는 어떠한 뒷받침도, 내재 가치([intrinsic value](http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/))도, 중앙발행자도 없이 가치를 유지하는 분산화된 P2P 온라인 통화(decentralized peer-to-peer online currency)로서의 비트코인이다. 지금까지 통화로서의 비트코인은 중앙 은행이 없는 화폐라는 정치적 관점과 크게 오르내리는 가격변동성 관점에서 사람들의 주목을 받아 왔다. 그러나 비트코인에는 Satoshi의 중대한 실험 - 작업증명(proof of work) 기반 블록체인에 의해 트랜잭션의 순서를 공공이(public) 합의한다는 또 다른 중요한 측면이 있다. 이러한 응용 프로그램으로서의 Bitcoin은 first-to-file시스템으로 설명할 수 있다. 예를 들어 만약 50BTC를 보유하고 있는 하나의 주체가 같은 50BTC를 A와 B로 동시에 보냈을 때 처음에 컨펌된(confirmed) 트랜잭션만 실행된다. 비트코인이 등장하기 이전에는 두 트랜잭션 중 어느 쪽이 먼저 이루어졌는지를 결정하는 본질적인 방법이 없었고, 이는 분산 디지털 통화의 발전을 방해했다. Satoshi의 블록체인은 이 문제를 최초로 신뢰할 수 있는 방법으로 해결했다. 요즘들어 사람들은 비트코인의 이러한 두번째 특징에 기반해서, 블록체인의 개념을 단순한 돈 이상의 것에 어떻게 적용할 수 있는가에 대해 주목하기 시작했다.

블록체인에 디지털 자산을 탑재하는 사례로는 사용자정의 화폐(custom currency) 또는 사용자정의 금융상품을 기록하거나(["colored coins"](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit)), 물리적 장치의 소유권을 기록하거나(["smart property"](htups://en.bitcoin.it/wiki/Smart_Property)), 도메인 네임 같은 대체 불가능한 자산(non-fungible asset)을 기록하는 것("Namecoin")등이 있다. 더 발전된 응용 프로그램으로서 분산화된 환전, 분산화된 금융 파생상품, p2p 도박, 분산화된 신분보관 및 평판시스템 등이 있다. 그리고 또 다른 중요한 연구 분야는 "smart contract" - 사전에 정해진 규칙에 따라 디지털 자산을 자동으로 이동시는 시스템이다. 가령, 다음과 같은 재무계약을 한다고 하자. "A는 하루에 화폐를 X만큼 인출할 수 있고, B는 하루에 Y까지 통화를 인출할 수 있다. A와 B가 함께라면 얼마든지 인출할 수 있다. 그리고 A는 B가 인출할 권리를 정지시킬 수 있다". 이 계약의 논리적인 확장이 분산화된 자율 조직([decentralized autonomous organizations](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/) (DAOs)) -  조직의 내부규칙이 프로그램 코딩되어 조직의 전체 자산을 자동으로 통제하는 장기적인 smart contract이다. Ethereum이 제공하려는 것은 완벽한 튜링완전(turing-complete) 프로그래밍 언어가 심어진 블록체인이다. 이 프로그래밍 언어는, 코딩된 규칙에 따라 '어떤 상태'를 다르게 변화시키는 기능(arbitrary state transition functions)이 포함된 "계약(contracts)"을 유저들이 작성할 수 있게 함으로써 앞서 설명한 시스템 구현을 가능하게 한다. 이 뿐만 아니라 우리가 아직 상상하지 못한 다른 많은 것들도 할 수 있다.

### Table of Contents

* [역사](#역사)
    * [Bitcoin As A State Transition System]
    * [Mining]
    * [Merkle Trees]
    * [Alternative Blockchain Applications]
    * [Scripting]
* [Ethereum]
    * [Ethereum Accounts]
    * [Messages and Transactions]
    * [Ethereum State Transition Function]
    * [Code Execution]
    * [Blockchain and Mining]
* [Applications]
    * [Token Systems]
    * [Financial derivatives]
    * [Identity and Reputation Systems]
    * [Decentralized File Storage]
    * [Decentralized Autonomous Organizations]
    * [Further Applications]
* [Miscellanea And Concerns]
    * [Modified GHOST Implementation]
    * [Fees]
    * [Computation And Turing-Completeness]
    * [Currency And Issuance]
    * [Mining Centralization]
    * [Scalability]
* [Conclusion]
* [References and Further Reading]


## 비트코인과 기존 개념들에 대한 소개

### 역사

분산화된 디지털 통화의 개념은, 재산등록 같은 어플리케이션과 마찬가지로 지난 수십년간 우리 주변에 있었다. 1980~90년대의 익명 e-cash 프로토콜은 주로 Chaumian blinding로 알려진 암호 프리미티브(cryptographic primitive)에 기반하였고 개인정보를 강력하게 보호하는 통화를 제공하였으나 중앙집권적인 중개인에 의존했기 때문에 견인력을 얻는데 실패했다. 1998년 Wei Dai의 [b-money](http://www.weidai.com/bmoney.txt)는 분산합의 뿐만 아니라 계산 퍼즐을 풀게 함으로서 돈을 만드는 아이디어를 최초로 제안하였지만 분산합의를 실제로 어떻게 구현할지에 대한 자세한 방법을 제시하지 못했다. 2005년에 Hall Finney는 "재사용 가능한 작업증명([reusable proofs of work](http://www.finney.org/~hal/rpow/))" 개념을 소개하였다. 이 시스템은 b-money의 아이디어에 Adam Back의 계산 난이도 해시캐시 퍼즐(computationally difficult Hashcash puzzles)을 조합한 것이었다. 그러나 이 과정의 마지막(backend)에 신뢰해야 하는 컴퓨팅(trusted computing)을 두고 그것에 의존함으로써, 이상을 구현하는데 또 다시 실패했다.

통화는 트랜잭션의 순서가 매우 중요한 first-to-file 애플리케이션이기 때문에 분산 통화는 분산화된 순서합의 방법이 반드시 필요하다. 복수집단 사이의 합의에 필요한 비잔티움 장애 허용(Byzantine-fault-tolerant)을 안전하게 구현하기 위한 많은 연구가 오랫동안 진행돼 왔지만, 비트코인 이전의 모든 프로토콜은 이 문제의 절반밖에 해결하지 못한 것이 사실이다. 기존 프로토콜들은 시스템에 참여하는 모든 참가자가 알려져 있다고 가정하고 있어서 "만약 N개의 집단이 참여하면 악의적인 행위자 수가 N/4가 될 때까지는 시스템이 견딜 수 있다"라는 형태로 보안 마진을 결정했다. 그러나 문제는 참가자들을 익명이라고 가정하면, 그러한 보안마진은 sybil attack - 한 공격자가 수천개의 가상 노드를 서버에 만들거나 봇넷을 생성하고 대다수 점유율을 강제로 얻어내기 위해 이 노드들을 이용하는 공격에 취약하다는 것이다. 

in progress..