### 차세대 스마트 컨트랙트와 탈중앙화된 어플리케이션 플랫폼 
(A Next-Generation Smart Contract and Decentralized Application Platform)

사토시 나카모토에 의해 2009년 개발된 비트코인은 종종 화폐와 통화분야에서 매우 근본적인 혁신으로 묘사되어 왔는데, 이것은 비트코인이 어떤 담보나 내재적인 가치를 가지지 않으면서도 또한 중앙화된 발행기관이나 통제기관도 없는 디지털 자산의 첫번째 사례였기 때문이다. 하지만 비트코인 실험의 더욱 중요한 측면은 비트코인을 떠받치고 있는 분산 합의 수단으로서의 블록체인 기술이며, 이에 대한 많은 관심이 급격하게 늘어나고 있다.

블록체인 기술을 이용한 대안적인 어플리케이션들에는 다음과 같은 것들이 자주 거론되고 있다. 사용자 정의 화폐와 금융상품을 블록체인 위에 표현하는 컬러드 코인("colored coins"), 물리적 대상의 소유권을 표현하는 스마트 자산("smart property"), 도메인 이름과 같은 비동질적 자산을 기록하는 네임코인("Namecoin"), 임의적인 계약규칙을 구현한 코드에 의해 다지털 자산을 관리하는 좀 더 복잡한 형태의 스마트 컨트랙트 ("smart contracts"), 더 나아가 블록체인을 기반으로 한 탈중앙화된 자율 조직( "decentralized autonomous organizations" , DAOs) 등이다.

이더리움이 제공하려는 것은 완벽한 튜링완전(turing-complete) 프로그래밍 언어가 심어진 블록체인이다. 이 프로그래밍 언어는, 코딩된 규칙에 따라 '어떤 상태'를 다르게 변화시키는 기능(arbitrary state transition functions)이 포함된 "계약(contracts)"을 유저들이 작성할 수 있게 함으로써 앞서 설명한 시스템들을 구현 가능하게 할  뿐만 아니라 우리가 아직 상상하지 못한 다른 많은 어플리케이션들도 매우 쉽게 만들 수 있도록 도와줄 것이다.

### 목차

* [역사](#history)
    * [상태변환시스템으로서의 비트코인](#bitcoin-as-a-state-transition-system)
    * [채굴](#mining)
    * [머클트리](#merkle-trees)
    * [블록체인 사용한 다른 사용사례](#alternative-blockchain-applications)
    * [스크립팅](#scripting)
* [이더리움](#ethereum)
    * [이더리움 어카운트](#ethereum-accounts)
    * [메시자와 트랜잭션](#messages-and-transactions)
    * [이더리움 상태변환함수](#ethereum-state-transition-function)
    * [코드 실행](#code-execution)
    * [블럭체인과 채굴](#blockchain-and-mining)
* [어플리케이션들](#applications)
    * [토큰 시스템](#token-systems)
    * [금융 파생상품](#financial-derivatives-and-stable-value-currencies)
    * [신원조회와 평판시스템](#identity-and-reputation-systems)
    * [탈중앙화된 파일 저장공간](#decentralized-file-storage)
    * [탈중앙화된 자율 조직](#decentralized-autonomous-organizations)
    * [추가적인 어플리케이션들](#further-applications)
* [기타 이슈들](#miscellanea-and-concerns)
    * [수정된 GHOST 도입](#modified-ghost-implementation)
    * [수수료](#fees)
    * [연산과 튜링완전성](#computation-and-turing-completeness)
    * [통화와 발행](#currency-and-issuance)
    * [채굴 중앙집중화](#mining-centralization)
    * [확장성](#scalability)
* [결론](#conclusion)
* [참고문헌과 추가 자료](#references-and-further-reading)

## 비트코인과 기존 개념들에 대한 소개(Introduction to Bitcoin and Existing Concepts)

### 역사(History)

분산화된 디지털 통화의 개념은, 재산등록 같은 대안 어플리케이션과 마찬가지로 지난 수십년간 우리 주변에 있었다. 1980~90년대의 익명 e-cash 프로토콜은 주로 ‘Chaumian blinding’으로 알려진 ‘로우레벨 암호 알고리듬(cryptographic primitive)’에 기반하였고 개인정보를 강력하게 보호하는 통화를 제공하였으나 중앙집권적인 중개인에 의존했기 때문에 별다른 주목을 받지 못했다. 1998년 Wei Dai의 [b-money](http://www.weidai.com/bmoney.txt)는 분산 합의와 계산 퍼즐을 풀게 하는 방식을 통해서 화폐를 발행하게 하는 아이디어를 최초로 제안하였지만 분산 합의를 실제로 어떻게 구현할지에 대한 자세한 방법은 제시하지 못했다. 2005년에 Hall Finney는 "재사용 가능한 작업증명([reusable proofs of work](http://www.finney.org/~hal/rpow/))" 개념을 소개하였다. 이 시스템은 b-money의 아이디어에 Adam Back의 ‘계산 난이도 해시캐시 퍼즐(computationally difficult Hashcash puzzles)’을 조합한 것이었다. 그러나 외부의 신뢰를 필요로 하는 컴퓨팅(trusted computing)을 그 기반에 둠으로써, 이상을 구현하는데에는 또 다시 실패했다. 2009년 사토시 나카모토에 의해 처음 실제적으로 구현된 탈중앙화된 화폐는 공개키 암호방식을 통한 소유권 관리를 위해 사용되던 기존의 알고리듬을 ‘작업 증명(proof of work)’이라고 알려진 합의 알고리듬과 결합함으로써 가능하게 되었다. 

작업증명이 기반하고 있는 작동방식은 매우 혁신적인 것이었는데, 이것은 두가지의 문제들을 동시에 해결하기 때문이다.  첫째, 이것은 간단하면서도 상당히 효과적인 합의 알고리듬을 제공해주었다.  즉, 네트워크 상에 있는 모든 노드들이 비트코인의 장부상태(state of the Bitcoin ledger)에 일어난 표준적 업데이트의 집합(a set of canonical updates)에 공동으로 동의할 수 있도록 해주었다는 것이다.  둘째, 이것은  누구나 합의 프로세스에 참여할 수 있도록 허용해줌으로써 합의결정권에 대한 정치적 문제를 해결할 수 있을 뿐만 아니라 동시에 시빌공격(sybil attacks)도 방어해줄 수 있는 메커니즘을 제공했다.  이것은 합의 프로세스에 대한 참여의 조건으로 ‘특정한 리스트에 등록된 주체이어야만 한다’라는 어떤 형식적 장벽대신에, 경제적 장벽 - 각 노드의 결정권의 크기를 그 노드의 계산능력에 직접적으로 비례시키는 방식으로 대체하는 것이었다.

이후로, 지분증명(proof of stake)이라는 새로운 방식의 합의 알고리듬이 등장했는데, 이는 각 노드가 가진 계산능력이 아니라 화폐의 보유량에 따라 각 노드의 결정권의 크기를 계산해야 한다는 것이다.  이 두 방식의 상대적인 장점들에 대한 논의는 이 백서의 범위를 벗어나는 것이지만, 양쪽 방법 모두 암호화화폐의 기반으로서 사용될 수 있다는 점은 지적해두고자 한다.

### 상태변환시스템으로서의 비트코인(Bitcoin As A State Transition System)

![statetransition.png](http://vitalik.ca/files/statetransition.png?2)

기술적인 관점에서 보았을 때, 비트코인과 같은 암호화 화폐의 장부는 하나의 상태변환시스템(state transition system)으로 생각해볼 수 있다. 이 시스템은, 현재 모든 비트코인의 소유권 현황으로 이루어진 하나의 “상태(state)” 와 이 현재 상태와 트랜잭션을 받아서 그 결과로써 새로운 상태를 출력해주는 “상태변환함수(state transition function)”로 구성되어 있다. 표준 은행 시스템에 비유하자면 상태는 모든계좌잔고표(balance sheet)이고 트랜잭션은 A에서 B로 $X를 송금하라는 요청이며, 상태변환함수에 의해 A의 계좌에서는 $X가 감소하고 B의 계좌에서는 $X가 증가한다. 만약 처음에 A의 계좌에 있는 금액이 $X 이하인 경우에는 상태변환함수가 에러를 리턴한다.
이러한 상태변환를 비트코인 장부에서는 다음과 같이 정의할 수 있다.

    APPLY(S,TX) -> S' or ERROR

은행 시스템 예시에서는 다음과 같다.

    APPLY({ Alice: $50, Bob: $50 },"send $20 from Alice to Bob") = { Alice: $30, Bob: $70 }

    APPLY({ Alice: $50, Bob: $50 },"send $70 from Alice to Bob") = ERROR

비트코인에서 "상태(state)"는 생성되었지만 아직 사용되지 않은 모든 코인들의 집합(기술적으로 표현하면 소비되지 않은 트랜잭션출력, UTXO(Unspent Transaction Outputs))이다. 각 UTXO들에는 각자의 코인금액이 표시되어 있고 이 UTXO의 소유자( 20byte의 주소로 정의되는 암호화된 공개키(public key)<sup>[1]</sup>)정보가 들어 있다. 트랜잭션은 하나 이상의 입력(inputs) 및 출력을 포함한다. 각 입력에는 보내는 쪽 지갑주소에서 선택된 기존 UTXO에 대한 참조정보와, 해당지갑주소에 대응되는 개인키(private key)가 생성한 암호화된 서명을 담고 있다. 그리고 각 출력들은 상태에 추가될 새로운 UTXO 정보를 가지고 있다.

상태변환함수 `APPLY(S,TX) -> S'` 는 대강 다음과 같이 정의할 수 있다.

1. TX의 각 입력에 대해 :
    * 만약 참조된 UTXO가 `S`에 없다면, 에러를 리턴.
    * 만약 서명이 UTXO의 소유자와 매치되지 않으면, 에러를 리턴.
2. 만약 입력에 사용된 UTXO들 금액의 합이 출력 UTXO들 금액의 합보다 작으면, 에러를 리턴.
3. 입력에 사용된 UTXO가 삭제되고 출력 UTXO가 추가된 `S`를 리턴.

여기서 1번의 첫번째 과정은 존재하지 않는 코인이 트랜잭션에 사용되는 것을 막기 위한 것이고 1번의 두번째 과정은 다른 사람의 코인이 트랜잭션에 사용되는 것을 막기 위한 것이다. 위 절차를 실제 비트코인 지불과정에 적용하면 다음과 같다. Alice가 Bob에게 11.7 BTC를 보내고 싶다고 가정하자. 먼저 Alice 지갑주소로부터 표시된 금액의 합이 적어도 11.7 BTC 이상인 UTXO의 집합을 찾는다. 실제 대부분의 경우에는 11.7 BTC를 정확히 바로 선택할 수 없다. Alice의 지갑주소에서 각각 6, 4, 2 BTC 가 표시된 3개의 UTXO를 참조할 수 있다고 하자. 이 3개의 UTXO가 트랜잭션의 input이 되고 2개의 output이 생성된다. Output 중 하나는 11.7 BTC가 표시된 새로운 UTXO이며 소유자는 Bob의 지갑주소가 된다. 그리고 다른 하나는 12(6+4+2) - 11.7 = 0.3 BTC의 "잔돈(change)"이 표시된 새로운 UTXO이며 소유자는 Alice 자신의 지갑주소가 된다.

### 채굴

![block_picture.jpg](http://vitalik.ca/files/block_picture.png)

만일 우리가 위에서 기술한 내용을 신뢰를 기반으로 하는 중앙집권화된 서비스 방식으로 구현하자면 매우 간단한 일이 될텐데, 왜냐하면 중앙 서버 하드드라이브에 상태변화의 과정을 저장만 하면 되기 때문이다.  그러나 비트코인에서는,  탈중앙화된 통화시스템을 구축하고자 하는 것이며, 이를 위해서는  모든 사람이 수긍할 수 있는 트랜잭션 순서 합의 시스템을 상태변화시스템과 결합해야만 한다.. 비트코인의 분산 합의 과정은 네트워크에 "블록(blocks)"이라 불리는 트랜잭션 패키지를 계속적으로 생성하고자 시도하는  노드들을 필요로 한다. 이 네트워크는 약 10분마다 하나의 블록을 생성하도록 계획되어 있고 각 블록은 타임스탬프, 논스(nonce), 이전 블록에 대한 참조(이전 블록의 해시), 그리고 이전 블록 이후에 발생한 모든 트랜잭션의 목록을 포함한다. 이 과정을 통해서 지속적으로 성장하는 블록체인이 생성되게 되는데,  비트코인 장부의 최신 상태(state)를 나타내기 위해 지속적인 업데이트가 이루어진다.

이 체계에서 하나의  블록이 유효한지 아닌지를 확인하기 위한 알고리즘은 다음과 같다.

1. 이 블록에 의해 참조되는 이전 블록이 존재하는지, 유효한지 확인한다.
2. 이 블록의 타임스탬프 값이 이전 블록의 타임스탬프 값보다 크면서 2시간 이내인지 확인한다.
3. 이 블록의 작업증명(proof of work)이 유효한지 확인한다.
4. `S[0]`를 이전 블록의 마지막 상태(state)가 되도록 설정한다.
5. `TX`를 `n`개의 트랜잭션을 가지는, 블록의 트랜잭션 목록으로 가정한다. 폐구간 `0...n-1`의 모든 i에 대해, `S[i+1] = APPLY(S[i], TX[i])`집합 중 어느 하나라도 에러를 리턴하면 거짓(false)을 리턴하며 종료한다.
6. 참(true)을 리턴하고, `S[n]`를 이 블록의 마지막 상태로 등록한다.

기본적으로 블록의 각 트랜잭션은 유효한 상태전환을 일으켜야 한다.. 여기서 상태가 블록내에 어떤 방법으로로 기록되지 않았다는 점에 주목해보자. 상태는 유효성을 검증하는 노드가  매번 계산해서 기억해야 할  완전히 추상적 것(abstraction)인데, 이것은  원시상태(genesis state)부터 해당 블록까지의 모든 트랜잭션을 순차적으로 적용함으로써 계산될 수 있다. 채굴자가 블록에 포함시키는 트랜잭션의 순서에 주목해보자. 만약 어떤 블록에 A와 B라는 두 트랜잭션이 있고 B가 A의 출력 UTXO를 소비한다고 하자. 이때 A가 B이전의 트랜잭션인 경우 그 블록은 유효하지만 그렇지 않으면 유효하지 않다.

블록 유효성 검증 알고리즘에서 특징적인  부분은 "작업증명(proof of work)" 의 조건 즉, 256 비트의 숫자로 표현되는 각 블록의 이중-SHA256 해시값이 동적으로 조정되는 목표값(이더리움 영문 백서를 작성하는 시점에서 대략 2<sup>187</sup>보다 반드시 작아야 된다는 조건이다. 작업증명의 목적은 블록 생성을 계산적으로 어렵게 만들어서 sybil 공격자들이 마음대로 전체 블록체인을 고치는 것을 방지하는 것이다. SHA256은 전혀 예측불가능한 유사난수 함수(pseudorandom function)로 설계되었기 때문에 유효 블록을 생성하기 위한 유일한 방법은 블록헤더의 논스(nonce) 값을 계속해서 증가시키면서, 생성되는 새로운 해시값이 위의 조건을 만족하는지 확인하는 과정을 반복하는 것 뿐이다. 
현재 목표값인 2<sup>187</sup>하에서 하나의 유효블록을 발견하기 위해서 평균적으로 264번의 시도를 해야만 한다.. 일반적으로 이 목표값은 매 2016개의 블록마다 네트워크에 의해 재조정되어서 네트워크의 현재 노드들이 평균적으로 10분마다 새로운 블록을 생성할 수 있도록 한다. 이러한 연산작업에 대한 보상으로 현 시점의 각 블록의 채굴자들은 25 BTC를 획득할 자격을 가진다. 그리고 출력금액보다 입력금액이 큰 트랜잭션이 있다면 그 차액을 "트랜잭션 수수료(transaction fee)"로 얻는다. 이것이 BTC가 발행되는 유일한 방법이며,  원시상태(genesis state)에는  아무런 코인이 포함되지 않았다.

채굴 목적을 더 잘 이해하기 위해서 악의적인 공격자가 있을 때 어떤 일이 발생하는지 알아보자. 비트코인 기저를 이루는 암호는 안전한 것으로 알려져 있다. 그러므로 공격자는 비트코인 시스템에서 암호에 의해 직접 보호되지 않는 부분인 트랜잭션 순서를 공격 목표로 잡을 것이다. 공격자의 전략은 매우 단순하다.

1. 어떤 상품(가급적이면 바로 전달되는 디지털 상품)을 구매하기 위해 판매자에게 100 BTC를 지불한다.
2. 상품이 전송되기를 기다린다.
3. 판매자에게 지불한 것과 같은 100 BTC를 공격자 자신에게 보내는 트랜잭션을 생성한다.(이중지불 시도)
4. 비트코인 네트워크가, 공격자 자신에게 보내는 트랜잭션이 판매자에게 지불하는 트랜잭션보다 먼저 수행된 것으로 인식하도록 한다.

1번 과정이 발생하고 몇 분 후에 몇몇 채굴자가 그 트랜잭션을 블록에 포함할 것이다. 이 블록 번호를 270000이라 하자. 대략 1시간 후에는 이 블록 다음의 체인에 5개의 블록들이 추가될 것이다. 이 5개의 블록들은 위 1번 트랜잭션을 간접적으로 가리킴으로써 "컨펌(confirming)"한다. 이 시점에서 판매자는 지불이 완료된 것으로 판단하고 상품을 전송할 것이다. 디지털 상품으로 가정했으므로 전송은 바로 끝난다. 이제 공격자는 판매자에게 보낸 것과 동일한 100 BTC를 공격자 자신에게 보내는 다른 트랜잭션을 생성한다. 만약 공격자가 그냥 단순하게 트랜잭션을 시도한다면, 채굴자들이 `APPLY(S,TX)`를 실행하고 이 `TX`는 상태에 더 이상 존재하지 않는 UTXO를 소비하려 한다는 것을 알아차리므로 이 트랜잭션은 진행되지 않는다. 그러므로 대신에, 같은 부모 블록 269999을 가리키지만 판매자에게 보낸 것을 대체하는 새로운 트랜잭션이 포함된 다른 버전의 블록 270000을 채굴함으로서 블록체인 "분기점(fork)"을 생성한다. 이 블록 정보는 원래 것과 다르므로 proof of work 가 다시 수행돼야 한다. 그리고 공격자의 새버전 블록 270000은 기존 270000과 다른 해시를 가지므로 원래 블록 270001부터 270005는 공격자의 블록을 가리키지 않는다. 그러므로 원래 체인과 공격자의 새로운 체인은 완전히 분리된다. 이러한 분기점에서 비트코인 네트워크의 규칙은 가장 긴 블록체인을 참으로 인식하는 것이다. 공격자가 자신의 체인에서 혼자 작업을 하는 동안 정당한 채굴자들은 원래의 270005체인에서 작업할 것이기 때문에 공격자 자신의 체인을 가장 길게 만들기 위해서는 네트워크의 다른 노드들의 계산능력 조합보다 큰 계산능력을 가져야 한다.(이를 51% attack이라 한다.)

### 머클트리

![SPV in bitcoin](https://www.ethereum.org/gh_wiki/spv_bitcoin.png)

_왼쪽: Merkle tree(머클트리)의 몇몇 노드만 보아도 곁가지(branch)의 유효성을 입증하기에 충분하다._

_오른쪽: Merkle tree의 어떤 부분을 바꾸려는 시도는 결국 상위 해시값 어딘가에 불일치를 만든다._

비트코인의 중요한 확장 기능은 블록이 여러 계층 구조(multi-layer data structure)에 저장된다는 것이다. 어떤 블록의 "해시(hash)"란 사실 블록헤더의 해시만을 의미한다. 이 블록헤더에는 타임스탬프, 논스(nonce), 이전 블록 해시, 그리고 블록에 포함된 모든 트랜잭션 정보에 의해 생성되는 Merkle tree의 루트 해시가 들어있는 200 바이트 정도의 데이터이다.. Merkle tree는 이진트리의 일종으로서 트리의 최하위에 위치하고 기저 데이터가 들어있는 수 많은 잎노드, 자기 자신 바로 하위에 있는 두 자식 노드의 해시로 구성된 중간 노드, 자기 자신 바로 하위에 있는 두 자식 중간노드의 해시로 구성되어 트리의 "최상위(top)"에 있는 하나의 루트 노드의 집합이다. Merkle tree의 목적은 어떤 블록의 데이터가 분리돼서 전달될 수 있도록 하는 것이다. 만약에 비트코인의 어떤 노드가 한 소스로부터 블록헤더만을 다운로드 받고, 이 블록헤더와 관계된 트랜잭션 정보는 다른 소스로부터 다운받아도 이 데이터들이 여전히 정확하다는 것이 보장된다. 이것이 가능한 이유는 Merkle tree에서 하위 노드들의 해시값이 상위 노드에 영향을 주기 때문에 어떤 악의적인 유저가 Merkle tree 최하위에 있는 트랜잭션 정보를 가짜로 바꿔치기 하면 상위 부모들의 해시값들이 변해서 결국 트리의 루트값이 바뀌므로, 결과적으로 이 블록의 해시가 달라지기 때문이다. 이렇게 되면 이 블록은 완전히 다른 블록으로 인식되게 되며, 이것은  유효하지 않은 작업증명을 가지고 있게 될 것이 확실시된다. 

Merkle tree 프로토콜은 비트코인 네트워크를 장기간 지속가능하게 만드는 기초가 된다. 비트코인 네트워크에서 각 블록의 모든 정보를 저장하고 처리하는 "완전노드(full node)"는 2014년 4월 기준으로 거의 15 GB의 디스크 공간을 필요로 하며 매달 1 GB 넘게 증가한다. 현재 데스크탑 컴퓨터 정도에서는  수용할 수 있지만 스마트폰에서는 불가능하다. 그리고 나중에는  소수의 사업체들이나 풀 노드를 유지할 수 있을 것이다.  반면 "단순화된 지불확인(simplified payment verification, SPV)"으로 알려진 프로토콜은 "가벼운 노드(light node)"라고 불리는 또 다른 형태의 노드를 가능하게 해준다. 가벼운 노드는 블록헤더를 다운로드하고 그 블록헤더에서 작업증명을  검증한다. 그리고 관련 트랜잭션들에 대한 "곁가지들(branches)"만을 다운로드 한다. 이렇게 전체 블록체인의 매우 작은 비율만을 다운로드 함에도 불구하고 강한 안전성을 보장하면서도, 임의의 트랜잭션의 상태 및 잔고 상태를 알아낼 수 있게 한다.

### 블록체인 기술을 이용한 다른 응용 사례 (Alternative Blockchain Applications)

블록체인의 근본 아이디어를 확장해 다른 개념으로 응용하려는 아이디어 역시 오랜 역사를 가지고 있다. 2005년 닉Szabo(발음표기 정리)는 "소유주 권한을 통한 재산권 보장"이라는 글을 발표했다. 그는 정주(homesteading), 불법점유, 지공주의(Georgism) 등의 개념을 포함한 정교한 틀을 설계해 누가 어떤 땅을 가지고 있느냐라는 등기 문제를 블록체인 기반 시스템으로 처리할 수 있음을 보였다. 그는 이것이 "데이터베이스 복제 기술의 새로운 발전"덕분에 가능해졌다고 말했다. 하지만, 불행히도 그 당시에는 쓸만한 효과적인 파일 복제 시스템이 없었다. 그래서 닉 싸보의 프로토콜은 실현되지 못했다. 하지만 2009년 이후, 비트코인 분권 합의 시스템이 발전하면서, 수 많은 대안 응용 사례가 빠르게 부각되기 시작했다.

* **네임코인** - 2010년에 만들어진 [네임코인](https://namecoin.org/)은 '탈중앙화된 명칭 등록 데이터베이스'라고 부르는 것이 가장 좋을 것이다. 토르,비트코인, 비트메시지와 같은 탈중앙화된 자율조직 프로토콜을 이용할 때, 사용자는 타인과 서로 교류하기 위해 각자의 계정을 구분해내야 한다. 하지만 현존하는 가능한 구별 방법은 1LW79wp5ZBqaHW1jL5TCiBCrhQYtHagUWy와 같은 식의 의사난수 해쉬를 이용하는 방식이었다. 이상적으로는, 사용자가 "george"같은 일상적인 이름을 계정 이름으로 갖는 것이 좋을 것이다. 하지만 이 때 문제는 어떤 사용자가 "george" 라는 이름을 계정을 만들수 있다면, 다른 누구도 똑같이 "george"라는 계정을 등록해 흉내낼 수 있다는 점이다. 유일한 해답은 선출원주의로, 먼저 등록한 사람이 성공하고 두 번째 등록한 사람은 실패하도록 하는 것이다. 이는 이미 비트코인 합의 규약에 완벽히 적용된 문제이기도 하다. 네임코인은 이런 아이디어를 응용한 가장 오래되고 가장 성공적인 명칭 등록 시스템이다

* **Colored coins** - the purpose of [colored coins](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit) is to serve as a protocol to allow people to create their own digital currencies - or, in the important trivial case of a currency with one unit, digital tokens, on the Bitcoin blockchain. In the colored coins protocol, one "issues" a new currency by publicly assigning a color to a specific Bitcoin UTXO, and the protocol recursively defines the color of other UTXO to be the same as the color of the inputs that the transaction creating them spent (some special rules apply in the case of mixed-color inputs). This allows users to maintain wallets containing only UTXO of a specific color and send them around much like regular bitcoins, backtracking through the blockchain to determine the color of any UTXO that they receive.
* **Metacoins** - the idea behind a metacoin is to have a protocol that lives on top of Bitcoin, using Bitcoin transactions to store metacoin transactions but having a different state transition function, `APPLY'`. Because the metacoin protocol cannot prevent invalid metacoin transactions from appearing in the Bitcoin blockchain, a rule is added that if `APPLY'(S,TX)` returns an error, the protocol defaults to `APPLY'(S,TX) = S`. This provides an easy mechanism for creating an arbitrary cryptocurrency protocol, potentially with advanced features that cannot be implemented inside of Bitcoin itself, but with a very low development cost since the complexities of mining and networking are already handled by the Bitcoin protocol. Metacoins have been used to implement some classes of financial contracts, name registration and decentralized exchange.

Thus, in general, there are two approaches toward building a consensus protocol: building an independent network, and building a protocol on top of Bitcoin. The former approach, while reasonably successful in the case of applications like Namecoin, is difficult to implement; each individual implementation needs to bootstrap an independent blockchain, as well as building and testing all of the necessary state transition and networking code. Additionally, we predict that the set of applications for decentralized consensus technology will follow a power law distribution where the vast majority of applications would be too small to warrant their own blockchain, and we note that there exist large classes of decentralized applications, particularly decentralized autonomous organizations, that need to interact with each other.

The Bitcoin-based approach, on the other hand, has the flaw that it does not inherit the simplified payment verification features of Bitcoin. SPV works for Bitcoin because it can use blockchain depth as a proxy for validity; at some point, once the ancestors of a transaction go far enough back, it is safe to say that they were legitimately part of the state. Blockchain-based meta-protocols, on the other hand, cannot force the blockchain not to include transactions that are not valid within the context of their own protocols. Hence, a fully secure SPV meta-protocol implementation would need to backward scan all the way to the beginning of the Bitcoin blockchain to determine whether or not certain transactions are valid. Currently, all "light" implementations of Bitcoin-based meta-protocols rely on a trusted server to provide the data, arguably a highly suboptimal result especially when one of the primary purposes of a cryptocurrency is to eliminate the need for trust.

### Scripting

Even without any extensions, the Bitcoin protocol actually does facilitate a weak version of a concept of "smart contracts". UTXO in Bitcoin can be owned not just by a public key, but also by a more complicated script expressed in a simple stack-based programming language. In this paradigm, a transaction spending that UTXO must provide data that satisfies the script. Indeed, even the basic public key ownership mechanism is implemented via a script: the script takes an elliptic curve signature as input, verifies it against the transaction and the address that owns the UTXO, and returns 1 if the verification is successful and 0 otherwise. Other, more complicated, scripts exist for various additional use cases. For example, one can construct a script that requires signatures from two out of a given three private keys to validate ("multisig"), a setup useful for corporate accounts, secure savings accounts and some merchant escrow situations. Scripts can also be used to pay bounties for solutions to computational problems, and one can even construct a script that says something like "this Bitcoin UTXO is yours if you can provide an SPV proof that you sent a Dogecoin transaction of this denomination to me", essentially allowing decentralized cross-cryptocurrency exchange.

However, the scripting language as implemented in Bitcoin has several important limitations:

* **Lack of Turing-completeness** - that is to say, while there is a large subset of computation that the Bitcoin scripting language supports, it does not nearly support everything. The main category that is missing is loops. This is done to avoid infinite loops during transaction verification; theoretically it is a surmountable obstacle for script programmers, since any loop can be simulated by simply repeating the underlying code many times with an if statement, but it does lead to scripts that are very space-inefficient. For example, implementing an alternative elliptic curve signature algorithm would likely require 256 repeated multiplication rounds all individually included in the code.
* **Value-blindness** - there is no way for a UTXO script to provide fine-grained control over the amount that can be withdrawn. For example, one powerful use case of an oracle contract would be a hedging contract, where A and B put in $1000 worth of BTC and after 30 days the script sends $1000 worth of BTC to A and the rest to B. This would require an oracle to determine the value of 1 BTC in USD, but even then it is a massive improvement in terms of trust and infrastructure requirement over the fully centralized solutions that are available now. However, because UTXO are all-or-nothing, the only way to achieve this is through the very inefficient hack of having many UTXO of varying denominations (eg. one UTXO of 2<sup>k</sup> for every k up to 30) and having O pick which UTXO to send to A and which to B.
* **Lack of state** - UTXO can either be spent or unspent; there is no opportunity for multi-stage contracts or scripts which keep any other internal state beyond that. This makes it hard to make multi-stage options contracts, decentralized exchange offers or two-stage cryptographic commitment protocols (necessary for secure computational bounties). It also means that UTXO can only be used to build simple, one-off contracts and not more complex "stateful" contracts such as decentralized organizations, and makes meta-protocols difficult to implement. Binary state combined with value-blindness also mean that another important application, withdrawal limits, is impossible.
* **Blockchain-blindness** - UTXO are blind to blockchain data such as the nonce, the timestamp and previous block hash. This severely limits applications in gambling, and several other categories, by depriving the scripting language of a potentially valuable source of randomness.

Thus, we see three approaches to building advanced applications on top of cryptocurrency: building a new blockchain, using scripting on top of Bitcoin, and building a meta-protocol on top of Bitcoin. Building a new blockchain allows for unlimited freedom in building a feature set, but at the cost of development time, bootstrapping effort and security. Using scripting is easy to implement and standardize, but is very limited in its capabilities, and meta-protocols, while easy, suffer from faults in scalability. With Ethereum, we intend to build an alternative framework that provides even larger gains in ease of development as well as even stronger light client properties, while at the same time allowing applications to share an economic environment and blockchain security.

## Ethereum

The intent of Ethereum is to create an alternative protocol for building decentralized applications, providing a different set of tradeoffs that we believe will be very useful for a large class of decentralized applications, with particular emphasis on situations where rapid development time, security for small and rarely used applications, and the ability of different applications to very efficiently interact, are important. Ethereum does this by building what is essentially the ultimate abstract foundational layer: a blockchain with a built-in Turing-complete programming language, allowing anyone to write smart contracts and decentralized applications where they can create their own arbitrary rules for ownership, transaction formats and state transition functions. A bare-bones version of Namecoin can be written in two lines of code, and other protocols like currencies and reputation systems can be built in under twenty. Smart contracts, cryptographic "boxes" that contain value and only unlock it if certain conditions are met, can also be built on top of the platform, with vastly more power than that offered by Bitcoin scripting because of the added powers of Turing-completeness, value-awareness, blockchain-awareness and state.

### Ethereum Accounts

In Ethereum, the state is made up of objects called "accounts", with each account having a 20-byte address and state transitions being direct transfers of value and information between accounts. An Ethereum account contains four fields:

* The **nonce**, a counter used to make sure each transaction can only be processed once
* The account's current **ether balance**
* The account's **contract code**, if present
* The account's **storage** (empty by default)

"Ether" is the main internal crypto-fuel of Ethereum, and is used to pay transaction fees. In general, there are two types of accounts: **externally owned accounts**, controlled by private keys, and **contract accounts**, controlled by their contract code. An externally owned account has no code, and one can send messages from an externally owned account by creating and signing a transaction; in a contract account, every time the contract account receives a message its code activates, allowing it to read and write to internal storage and send other messages or create contracts in turn.

Note that "contracts" in Ethereum should not be seen as something that should be "fulfilled" or "complied with"; rather, they are more like "autonomous agents" that live inside of the Ethereum execution environment, always executing a specific piece of code when "poked" by a message or transaction, and having direct control over their own ether balance and their own key/value store to keep track of persistent variables.

### Messages and Transactions

The term "transaction" is used in Ethereum to refer to the signed data package that stores a message to be sent from an externally owned account. Transactions contain:

* The recipient of the message
* A signature identifying the sender
* The amount of ether to transfer from the sender to the recipient 
* An optional data field
* A `STARTGAS` value, representing the maximum number of computational steps the transaction execution is allowed to take
* A `GASPRICE` value, representing the fee the sender pays per computational step

The first three are standard fields expected in any cryptocurrency. The data field has no function by default, but the virtual machine has an opcode using which a contract can access the data; as an example use case, if a contract is functioning as an on-blockchain domain registration service, then it may wish to interpret the data being passed to it as containing two "fields", the first field being a domain to register and the second field being the IP address to register it two. The contract would read these values from the message data and appropriately place them in storage.

The `STARTGAS` and `GASPRICE` fields are crucial for Ethereum's anti-denial of service model. In order to prevent accidental or hostile infinite loops or other computational wastage in code, each transaction is required to set a limit to how many computational steps of code execution it can use. The fundamental unit of computation is "gas"; usually, a computational step costs 1 gas, but some operations cost higher amounts of gas because they are more computationally expensive, or increase the amount of data that must be stored as part of the state. There is also a fee of 5 gas for every byte in the transaction data. The intent of the fee system is to require an attacker to pay proportionately for every resource that they consume, including computation, bandwidth and storage; hence, any transaction that leads to the network consuming a greater amount of any of these resources must have a gas fee roughly proportional to the increment.

### Messages

Contracts have the ability to send "messages" to other contracts. Messages are virtual objects that are never serialized and exist only in the Ethereum execution environment. A message contains:

* The sender of the message (implicit)
* The recipient of the message
* The amount of ether to transfer alongside the message
* An optional data field
* A `STARTGAS` value

Essentially, a message is like a transaction, except it is produced by a contract and not an external actor. A message is produced when a contract currently executing code executes the `CALL` opcode, which produces and executes a message. Like a transaction, a message leads to the recipient account running its code. Thus, contracts can have relationships with other contracts in exactly the same way that external actors can.

Note that the gas allowance assigned by a transaction or contract applies to the total gas consumed by that transaction and all sub-executions. For example, if an external actor A sends a transaction to B with 1000 gas, and B consumes 600 gas before sending a message to C, and the internal execution of C consumes 300 gas before returning, then B can spend another 100 gas before running out of gas.

### Ethereum State Transition Function

![ethertransition.png](http://vitalik.ca/files/ethertransition.png?1)

The Ethereum state transition function, `APPLY(S,TX) -> S'` can be defined as follows:

1. Check if the transaction is well-formed (ie. has the right number of values), the signature is valid, and the nonce matches the nonce in the sender's account. If not, return an error.
2. Calculate the transaction fee as `STARTGAS * GASPRICE`, and determine the sending address from the signature. Subtract the fee from the sender's account balance and increment the sender's nonce. If there is not enough balance to spend, return an error.
3. Initialize `GAS = STARTGAS`, and take off a certain quantity of gas per byte to pay for the bytes in the transaction.
4. Transfer the transaction value from the sender's account to the receiving account. If the receiving account does not yet exist, create it. If the receiving account is a contract, run the contract's code either to completion or until the execution runs out of gas.
5. If the value transfer failed because the sender did not have enough money, or the code execution ran out of gas, revert all state changes except the payment of the fees, and add the fees to the miner's account.
6. Otherwise, refund the fees for all remaining gas to the sender, and send the fees paid for gas consumed to the miner.

For example, suppose that the contract's code is:

    if !self.storage[calldataload(0)]:
        self.storage[calldataload(0)] = calldataload(32)

Note that in reality the contract code is written in the low-level EVM code; this example is written in Serpent, one of our high-level languages, for clarity, and can be compiled down to EVM code. Suppose that the contract's storage starts off empty, and a transaction is sent with 10 ether value, 2000 gas, 0.001 ether gasprice, and 64 bytes of data, with bytes 0-31 representing the number `2` and bytes 32-63 representing the string `CHARLIE`. The process for the state transition function in this case is as follows:

1. Check that the transaction is valid and well formed.
2. Check that the transaction sender has at least 2000 * 0.001 = 2 ether. If it is, then subtract 2 ether from the sender's account.
3. Initialize gas = 2000; assuming the transaction is 170 bytes long and the byte-fee is 5, subtract 850 so that there is 1150 gas left.
3. Subtract 10 more ether from the sender's account, and add it to the contract's account.
4. Run the code. In this case, this is simple: it checks if the contract's storage at index `2` is used, notices that it is not, and so it sets the storage at index `2` to the value `CHARLIE`. Suppose this takes 187 gas, so the remaining amount of gas is 1150 - 187 = 963
5. Add 963 * 0.001 = 0.963 ether back to the sender's account, and return the resulting state.

If there was no contract at the receiving end of the transaction, then the total transaction fee would simply be equal to the provided `GASPRICE` multiplied by the length of the transaction in bytes, and the data sent alongside the transaction would be irrelevant.

Note that messages work equivalently to transactions in terms of reverts: if a message execution runs out of gas, then that message's execution, and all other executions triggered by that execution, revert, but parent executions do not need to revert. This means that it is "safe" for a contract to call another contract, as if A calls B with G gas then A's execution is guaranteed to lose at most G gas. Finally, note that there is an opcode, `CREATE`, that creates a contract; its execution mechanics are generally similar to `CALL`, with the exception that the output of the execution determines the code of a newly created contract.

### Code Execution

The code in Ethereum contracts is written in a low-level, stack-based bytecode language, referred to as "Ethereum virtual machine code" or "EVM code". The code consists of a series of bytes, where each byte represents an operation. In general, code execution is an infinite loop that consists of repeatedly carrying out the operation at the current program counter (which begins at zero) and then incrementing the program counter by one, until the end of the code is reached or an error or `STOP` or `RETURN` instruction is detected. The operations have access to three types of space in which to store data:

* The **stack**, a last-in-first-out container to which values can be pushed and popped
* **Memory**, an infinitely expandable byte array
* The contract's long-term **storage**, a key/value store. Unlike stack and memory, which reset after computation ends, storage persists for the long term.

The code can also access the value, sender and data of the incoming message, as well as block header data, and the code can also return a byte array of data as an output.

The formal execution model of EVM code is surprisingly simple. While the Ethereum virtual machine is running, its full computational state can be defined by the tuple `(block_state, transaction, message, code, memory, stack, pc, gas)`, where `block_state` is the global state containing all accounts and includes balances and storage. At the start of every round of execution, the current instruction is found by taking the `pc`th byte of `code` (or 0 if `pc >= len(code)`), and each instruction has its own definition in terms of how it affects the tuple. For example, `ADD` pops two items off the stack and pushes their sum, reduces `gas` by 1 and increments `pc` by 1, and `SSTORE` pushes the top two items off the stack and inserts the second item into the contract's storage at the index specified by the first item. Although there are many ways to optimize Ethereum virtual machine execution via just-in-time compilation, a basic implementation of Ethereum can be done in a few hundred lines of code.

### Blockchain and Mining

![apply_block_diagram.png](http://vitalik.ca/files/apply_block_diagram.png)

The Ethereum blockchain is in many ways similar to the Bitcoin blockchain, although it does have some differences. The main difference between Ethereum and Bitcoin with regard to the blockchain architecture is that, unlike Bitcoin, Ethereum blocks contain a copy of both the transaction list and the most recent state. Aside from that, two other values, the block number and the difficulty, are also stored in the block. The basic block validation algorithm in Ethereum is as follows:

1. Check if the previous block referenced exists and is valid.
2. Check that the timestamp of the block is greater than that of the referenced previous block and less than 15 minutes into the future
3. Check that the block number, difficulty, transaction root, uncle root and gas limit (various low-level Ethereum-specific concepts) are valid.
4. Check that the proof of work on the block is valid.
5. Let `S[0]` be the state at the end of the previous block.
6. Let `TX` be the block's transaction list, with `n` transactions. For all in in `0...n-1`, set `S[i+1] = APPLY(S[i],TX[i])`. If any applications returns an error, or if the total gas consumed in the block up until this point exceeds the `GASLIMIT`, return an error. 
7. Let `S_FINAL` be `S[n]`, but adding the block reward paid to the miner.
8. Check if the Merkle tree root of the state `S_FINAL` is equal to the final state root provided in the block header. If it is, the block is valid; otherwise, it is not valid.

The approach may seem highly inefficient at first glance, because it needs to store the entire state with each block, but in reality efficiency should be comparable to that of Bitcoin. The reason is that the state is stored in the tree structure, and after every block only a small part of the tree needs to be changed. Thus, in general, between two adjacent blocks the vast majority of the tree should be the same, and therefore the data can be stored once and referenced twice using pointers (ie. hashes of subtrees). A special kind of tree known as a "Patricia tree" is used to accomplish this, including a modification to the Merkle tree concept that allows for nodes to be inserted and deleted, and not just changed, efficiently.  Additionally, because all of the state information is part of the last block, there is no need to store the entire blockchain history - a strategy which, if it could be applied to Bitcoin, can be calculated to provide 5-20x savings in space.

A commonly asked question is "where" contract code is executed, in terms of physical hardware. This has a simple answer: the process of executing contract code is part of the definition of the state transition function, which is part of the block validation algorithm, so if a transaction is added into block `B` the code execution spawned by that transaction will be executed by all nodes, now and in the future, that download and validate block `B`. 

## Applications

In general, there are three types of applications on top of Ethereum. The first category is financial applications, providing users with more powerful ways of managing and entering into contracts using their money. This includes sub-currencies, financial derivatives, hedging contracts, savings wallets, wills, and ultimately even some classes of full-scale employment contracts. The second category is semi-financial applications, where money is involved but there is also a heavy non-monetary side to what is being done; a perfect example is self-enforcing bounties for solutions to computational problems. Finally, there are applications such as online voting and decentralized governance that are not financial at all.

### Token Systems

On-blockchain token systems have many applications ranging from sub-currencies representing assets such as USD or gold to company stocks, individual tokens representing smart property, secure unforgeable coupons, and even token systems with no ties to conventional value at all, used as point systems for incentivization. Token systems are surprisingly easy to implement in Ethereum. The key point to understand is that all a currency, or token system, fundamentally is is a database with one operation: subtract X units from A and give X units to B, with the proviso that (i) X had at least X units before the transaction and (2) the transaction is approved by A. All that it takes to implement a token system is to implement this logic into a contract.

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

Such a contract would have significant potential in crypto-commerce. One of the main problems cited about cryptocurrency is the fact that it's volatile; although many users and merchants may want the security and convenience of dealing with cryptographic assets, they many not wish to face that prospect of losing 23% of the value of their funds in a single day. Up until now, the most commonly proposed solution has been issuer-backed assets; the idea is that an issuer creates a sub-currency in which they have the right to issue and revoke units, and provide one unit of the currency to anyone who provides them (offline) with one unit of a specified underlying asset (eg. gold, USD). The issuer then promises to provide one unit of the underlying asset to anyone who sends back one unit of the crypto-asset. This mechanism allows any non-cryptographic asset to be "uplifted" into a cryptographic asset, provided that the issuer can be trusted.

In practice, however, issuers are not always trustworthy, and in some cases the banking infrastructure is too weak, or too hostile, for such services to exist. Financial derivatives provide an alternative. Here, instead of a single issuer providing the funds to back up an asset, a decentralized market of speculators, betting that the price of a cryptographic reference asset (eg. ETH) will go up, plays that role. Unike issuers, speculators have no option to default on their side of the bargain because the hedging contract holds their funds in escrow. Note that this approach is not fully decentralized, because a trusted source is still needed to provide the price ticker, although arguably even still this is a massive improvement in terms of reducing infrastructure requirements (unlike being an issuer, issuing a price feed requires no licenses and can likely be categorized as free speech) and reducing the potential for fraud.

### Identity and Reputation Systems

The earliest alternative cryptocurrency of all, [Namecoin](http://namecoin.org/), attempted to use a Bitcoin-like blockchain to provide a name registration system, where users can register their names in a public database alongside other data. The major cited use case is for a [DNS](http://en.wikipedia.org/wiki/Domain_Name_System) system, mapping domain names like "bitcoin.org" (or, in Namecoin's case, "bitcoin.bit") to an IP address. Other use cases include email authentication and potentially more advanced reputation systems. Here is the basic contract to provide a Namecoin-like name registration system on Ethereum:

    def register(name, value):
        if !self.storage[name]:
            self.storage[name] = value

The contract is very simple; all it is is a database inside the Ethereum network that can be added to, but not modified or removed from. Anyone can register a name with some value, and that registration then sticks forever. A more sophisticated name registration contract will also have a "function clause" allowing other contracts to query it, as well as a mechanism for the "owner" (ie. the first registerer) of a name to change the data or transfer ownership. One can even add reputation and web-of-trust functionality on top.

### Decentralized File Storage

Over the past few years, there have emerged a number of popular online file storage startups, the most prominent being Dropbox, seeking to allow users to upload a backup of their hard drive and have the service store the backup and allow the user to access it in exchange for a monthly fee. However, at this point the file storage market is at times relatively inefficient; a cursory look at various [existing solutions](http://online-storage-service-review.toptenreviews.com/) shows that, particularly at the "uncanny valley" 20-200 GB level at which neither free quotas nor enterprise-level discounts kick in, monthly prices for mainstream file storage costs are such that you are paying for more than the cost of the entire hard drive in a single month. Ethereum contracts can allow for the development of a decentralized file storage ecosystem, where individual users can earn small quantities of money by renting out their own hard drives and unused space can be used to further drive down the costs of file storage. 

The key underpinning piece of such a device would be what we have termed the "decentralized Dropbox contract". This contract works as follows. First, one splits the desired data up into blocks, encrypting each block for privacy, and builds a Merkle tree out of it. One then makes a contract with the rule that, every N blocks, the contract would pick a random index in the Merkle tree (using the previous block hash, accessible from contract code, as a source of randomness), and give X ether to the first entity to supply a transaction with a simplified payment verification-like proof of ownership of the block at that particular index in the tree. When a user wants to re-download their file, they can use a micropayment channel protocol (eg. pay 1 szabo per 32 kilobytes) to recover the file; the most fee-efficient approach is for the payer not to publish the transaction until the end, instead replacing the transaction with a slightly more lucrative one with the same nonce after every 32 kilobytes.

An important feature of the protocol is that, although it may seem like one is trusting many random nodes not to decide to forget the file, one can reduce that risk down to near-zero by splitting the file into many pieces via secret sharing, and watching the contracts to see each piece is still in some node's possession. If a contract is still paying out money, that provides a cryptographic proof that someone out there is still storing the file. 

### Decentralized Autonomous Organizations

The general concept of a "decentralized autonomous organization" is that of a virtual entity that has a certain set of members or shareholders which, perhaps with a 67% majority, have the right to spend the entity's funds and modify its code. The members would collectively decide on how the organization should allocate its funds. Methods for allocating a DAO's funds could range from bounties, salaries to even more exotic mechanisms such as an internal currency to reward work. This essentially replicates the legal trappings of a traditional company or nonprofit but using only cryptographic blockchain technology for enforcement. So far much of the talk around DAOs has been around the "capitalist" model of a "decentralized autonomous corporation" (DAC) with dividend-receiving shareholders and tradable shares; an alternative, perhaps described as a "decentralized autonomous community", would have all members have an equal share in the decision making and require 67% of existing members to agree to add or remove a member. The requirement that one person can only have one membership would then need to be enforced collectively by the group.

A general outline for how to code a DAO is as follows. The simplest design is simply a piece of self-modifying code that changes if two thirds of members agree on a change. Although code is theoretically immutable, one can easily get around this and have de-facto mutability by having chunks of the code in separate contracts, and having the address of which contracts to call stored in the modifiable storage. In a simple implementation of such a DAO contract, there would be three transaction types, distinquished by the data provided in the transaction:

* `[0,i,K,V]` to register a proposal with index `i` to change the address at storage index `K` to value `V`
* `[0,i]` to register a vote in favor of proposal `i`
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

The "Greedy Heavist Observed Subtree" (GHOST) protocol is an innovation first introduced by Yonatan Sompolinsky and Aviv Zohar in [December 2013](http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf). The motivation behind GHOST is that blockchains with fast confirmation times currently suffer from reduced security due to a high stale rate - because blocks take a certain time to propagate through the network, if miner A mines a block and then miner B happens to mine another block before miner A's block propagates to B, miner B's block will end up wasted and will not contribute to network security. Furthermore, there is a centralization issue: if miner A is a mining pool with 30% hashpower and B has 10% hashpower, A will have a risk of producing a stale block 70% of the time (since the other 30% of the time A produced the last block and so will get mining data immediately) whereas B will have a risk of producing a stale block 90% of the time. Thus, if the block interval is short enough for the stale rate to be high, A will be substantially more efficient simply by virtue of its size. With these two effects combined, blockchains which produce blocks quickly are very likely to lead to one mining pool having a large enough percentage of the network hashpower to have de facto control over the mining process.

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
2. There do exist nonmining full nodes.
3. The mining power distribution may end up radically inegalitarian in practice.
4. Speculators, political enemies and crazies whose utility function includes causing harm to the network do exist, and they can cleverly set up contracts where their cost is much lower than the cost paid by other verifying nodes.

(1) provides a tendency for the miner to include fewer transactions, and (2) increases `NC`; hence, these two effects at least partially cancel each other out. (3) and (4) are the major issue; to solve them we simply institute a floating cap: no block can have more operations than `BLK_LIMIT_FACTOR` times the long-term exponential moving average. Specifically:

    blk.oplimit = floor((blk.parent.oplimit * (EMAFACTOR - 1) + floor(parent.opcount * BLK_LIMIT_FACTOR)) / EMA_FACTOR)

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

The Ethereum network includes its own built-in currency, ether, which serves the dual purpose of providing a primary liquidity layer to allow for efficient exchange between various types of digital assets and, more importantly, of providing a mechanism for paying transaction fees. For convenience and to avoid future argument (see the current mBTC/uBTC/satoshi debate in Bitcoin), the denominations will be pre-labelled:

* 1: wei
* 10<sup>12</sup>: szabo
* 10<sup>15</sup>: finney
* 10<sup>18</sup>: ether

This should be taken as an expanded version of the concept of "dollars" and "cents" or "BTC" and "satoshi". In the near future, we expect "ether" to be used for ordinary transactions, "finney" for microtransactions and "szabo" and "wei" for technical discussions around fees and protocol implementation; the remaining denominations may become useful later and should not be included in clients at this point.

The issuance model will be as follows:

* Ether will be released in a currency sale at the price of 1000-2000 ether per BTC, a mechanism intended to fund the Ethereum organization and pay for development that has been used with success by other platforms such as Mastercoin and NXT. Earlier buyers will benefit from larger discounts. The BTC received from the sale will be used entrirely to pay salaries and bounties to developers and invested into various for-profit and non-profit projects in the Ethereum and cryptocurrency ecosystem.
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

![SPV in bitcoin](https://www.ethereum.org/gh_wiki/inflation.svg)

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

The problem with such a large blockchain size is centralization risk. If the blockchain size increases to, say, 100 TB, then the likely scenario would be that only a very small number of large businesses would run full nodes, with all regular users using light SPV nodes. In such a situation, there arises the potential concern that the full nodes could band together and all agree to cheat in some profitable fashion (eg. change the block reward, give themselves BTC). Light nodes would have no way of detecting this immediately. Of course, at least one honest full node would likely exist, and after a few hours information about the fraud would trickle out through channels like Reddit, but at that point it would be too late: it would be up to the ordinary users to organize an effort to blacklist the given blocks, a massive and likely infeasible coordination problem on a similar scale as that of pulling off a successful 51% attack. In the case of Bitcoin, this is currently a problem, but there exists a blockchain modification [suggested by Peter Todd](http://sourceforge.net/p/bitcoin/mailman/message/31709140/) which will alleviate this issue.

In the near term, Ethereum will use two additional strategies to cope with this problem. First, because of the blockchain-based mining algorithms, at least every miner will be forced to be a full node, creating a lower bound on the number of full nodes. Second and more importantly, however, we will include an intermediate state tree root in the blockchain after processing each transaction. Even if block validation is centralized, as long as one honest verifying node exists, the centralization problem can be circumvented via a verification protocol. If a miner publishes an invalid block, that block must either be badly formatted, or the state `S[n]` is incorrect. Since `S[0]` is known to be correct, there must be some first state `S[i]` that is incorrect where `S[i-1]` is correct. The verifying node would provide the index `i`, along with a "proof of invalidity" consisting of the subset of Patricia tree nodes needing to process `APPLY(S[i-1],TX[i]) -> S[i]`. Nodes would be able to use those nodes to run that part of the computation, and see that the `S[i]` generated does not match the `S[i]` provided.

Another, more sophisticated, attack would involve the malicious miners publishing incomplete blocks, so the full information does not even exist to determine whether or not blocks are valid. The solution to this is a challenge-response protocol: verification nodes issue "challenges" in the form of target transaction indices, and upon receiving a node a light node treats the block as untrusted until another node, whether the miner or another verifier, provides a subset of Patricia nodes as a proof of validity.

## Conclusion

The Ethereum protocol was originally conceived as an upgraded version of a cryptocurrency, providing advanced features such as on-blockchain escrow, withdrawal limits, financial contracts, gambling markets and the like via a highly generalized programming language. The Ethereum protocol would not "support" any of the applications directly, but the existence of a Turing-complete programming language means that arbitrary contracts can theoretically be created for any transaction type or application. What is more interesting about Ethereum, however, is that the Ethereum protocol moves far beyond just currency. Protocols around decentralized file storage, decentralized computation and decentralized prediction markets, among dozens of other such concepts, have the potential to substantially increase the efficiency of the computational industry, and provide a massive boost to other peer-to-peer protocols by adding for the first time an economic layer. Finally, there is also a substantial array of applications that have nothing to do with money at all.

The concept of an arbitrary state transition function as implemented by the Ethereum protocol provides for a platform with unique potential; rather than being a closed-ended, single-purpose protocol intended for a specific array of applications in data storage, gambling or finance, Ethereum is open-ended by design, and we believe that it is extremely well-suited to serving as a foundational layer for a very large number of both financial and non-financial protocols in the years to come.

## Notes and Further Reading

#### Notes

1. A sophisticated reader may notice that in fact a Bitcoin address is the hash of the elliptic curve public key, and not the public key itself. However, it is in fact perfectly legitimate cryptographic terminology to refer to the pubkey hash as a public key itself. This is because Bitcoin's cryptography can be considered to be a custom digital signature algorithm, where the public key consists of the hash of the ECC pubkey, the signature consists of the ECC pubkey concatenated with the ECC signature, and the verification algorithm involves checking the ECC pubkey in the signature against the ECC pubkey hash provided as a public key and then verifying the ECC signature against the ECC pubkey.
2. Technically, the median of the 11 previous blocks.
3. Internally, 2 and "CHARLIE" are both numbers, with the latter being in big-endian base 256 representation. Numbers can be at least 0 and at most 2<sup>256</sup>-1.

#### Further Reading

1. Intrinsic value: http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/
2. Smart property: https://en.bitcoin.it/wiki/Smart_Property
3. Smart contracts: https://en.bitcoin.it/wiki/Contracts
4. B-money: http://www.weidai.com/bmoney.txt
5. Reusable proofs of work: http://www.finney.org/~hal/rpow/ 
6. Secure property titles with owner authority: http://szabo.best.vwh.net/securetitle.html
7. Bitcoin whitepaper: http://bitcoin.org/bitcoin.pdf
8. Namecoin: https://namecoin.org/
9. Zooko's triangle: http://en.wikipedia.org/wiki/Zooko's_triangle
10. Colored coins whitepaper: https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit
11. Mastercoin whitepaper: https://github.com/mastercoin-MSC/spec
12. Decentralized autonomous corporations, Bitcoin Magazine: http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/
13. Simplified payment verification: https://en.bitcoin.it/wiki/Scalability#Simplifiedpaymentverification
14. Merkle trees: http://en.wikipedia.org/wiki/Merkle_tree
15. Patricia trees: http://en.wikipedia.org/wiki/Patricia_tree
16. GHOST: http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf
17. StorJ and Autonomous Agents, Jeff Garzik: http://garzikrants.blogspot.ca/2013/01/storj-and-bitcoin-autonomous-agents.html
18. Mike Hearn on Smart Property at Turing Festival: http://www.youtube.com/watch?v=Pu4PAMFPo5Y
19. Ethereum RLP: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP
20. Ethereum Merkle Patricia trees: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree
21. Peter Todd on Merkle sum trees: http://sourceforge.net/p/bitcoin/mailman/message/31709140/