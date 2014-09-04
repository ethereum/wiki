### 차세대 스마트 컨트랙트와 분산(decentralized) 애플리케이션 플랫폼

Satoshi Nakamoto가 2009년 1월 비트코인 블록체인을 처음 세상에 내보냈을 때, 혁명적이면서 시험되지 않은 두 가지 컨셉을 동시에 제시했다. 첫번째는 어떠한 뒷받침도, 내재 가치(intrinsic value)도, 중앙발행자도 없이 가치를 유지하는 분산(decentralized) P2P(peer-to-peer) 네트워크 통화로서의 비트코인이다. 지금까지 통화로서의 비트코인은 중앙 은행이 없는 화폐라는 정치적 관점과 크게 오르내리는 가격변동성 관점에서 사람들의 주목을 받아 왔다. 그러나 비트코인에는 Satoshi의 중대한 실험 - 작업증명(proof of work) 기반 블록체인에 의해 트랜잭션의 순서를 공적(public)으로 합의하는 개념이라는 또 다른 중요한 측면이 있다. 애플리케이션으로서의 Bitcoin은 first-to-file시스템으로 설명할 수 있다. 예를 들어서 만약 50BTC를 보유하고 있는 하나의 주체가 같은 50BTC를 A와 B로 동시에 보냈을 때 처음에 컨펌된(confirmed) 트랜잭션만 실행된다. 비트코인이 등장하기 이전에는 두 트랜잭션 중 어느 쪽이 먼저 이루어졌는지를 결정하는 본질적인 방법이 없었고, 이는 분산 디지털 통화의 발전을 방해했다. Satoshi의 블록체인은 이 문제를 최초로 해결한 신뢰할만한 방법이다. 요즘들어 사람들은 비트코인의 이러한 두번째 특징에 기반해서, 블록체인의 개념을 단순한 돈 이상의 것에 어떻게 적용할 수 있는가에 대해 주목하기 시작했다.

블록체인에 디지털 자산을 탑재하는 사례로는 사용자정의(custom) 화폐 혹은 금융상품을 기록하거나(colored coins), 물리적 장치의 소유권을 기록하거나(smart property), 도메인 네임 같은 대체 불가능한 자산을 기록하는 것(Namecoin)등이 있다. 더 발전된 어플리케이션으로서 분산화된 환전(decentralized exchange), 금융 파생상품, p2p 도박, 신분보관 시스템 및 평판시스템 등이 있다. 그리고 또 다른 중요한 연구 분야는 "smart contract" - 사전에 정해진 임의의 규칙에 따라 디지털 자산을 자동으로 이동시는 시스템이다. 가령, 다음과 같은 재무계약을 한다고 하자. "A는 하루에 화폐를 X만큼 인출할 수 있고, B는 하루에 Y까지 통화를 인출할 수 있다. A와 B가 함께라면 얼마든지 인출할 수 있다. 그리고 A는 B가 인출할 권리를 정지시킬 수 있다". 이 계약의 논리적인 확장이 분산화된 자치 조직(decentralized autonomous organizations, DAOs) -  조직의 내부규칙이 프로그램 코딩되어 조직의 전체 자산을 자동으로 통제하는 장기적인 smart contract이다. Ethereum이 제공하려는 것은 튜링 완전(turing-complete) 프로그래밍 언어가 심어진 블록체인이다. 이 프로그래밍 언어는 ethereum 유저들이 단지 몇 줄의 코딩을 함으로써 '어떤 상태'를 코딩된 규칙에 따라 변화시키는 함수(arbitrary state transition functions)를 내재한 계약(contracts)을 만들 수 있게 하여 앞서 설명한 시스템 구현을 가능케 한다. 그리고 우리가 아직 상상하지 못한 다른 많은 것들도 할 수 있다.

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

분산화된 디지털 통화의 개념은, 재산등록 같은 어플리케이션과 마찬가지로 지난 수십년간 우리 주변에 있었다. 1980~90년대의 익명 e-cash 프로토콜은 주로 Chaumian blinding로 알려진 암호 프리미티브(cryptographic primitive)에 기반하고 있었고 개인정보를 강력하게 보호하는 통화를 제공하였으나 중앙집권적인 중개인에 의존했기 때문에 견인력을 얻는데 실패했다. 1998년 Wei Dai의 b-money는 분산합의 뿐만 아니라 계산 퍼즐을 풀게 함으로서 돈을 만드는 아이디어를 최초로 제안하였지만 분산합의를 실제로 어떻게 구현할지에 대한 자세한 방법을 제시하지 못했다. 2005년에 Hall Finney는 "재사용 가능한 작업증명(reusable proofs of work)" 개념을 소개하였다. 이 시스템은 b-money의 아이디어에 Adam Back의 계산 난이도 해시캐시 퍼즐(computationally difficult Hashcash puzzles)을 조합한 것이었다. 그러나 백엔드의 신뢰받는 컴퓨팅(trusted computing)에 의존함으로써, 이상적인 상황을 만드는데 또 다시 실패했다.

in progress..