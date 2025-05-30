# KIP의 핵심 거버넌스 변화

카이아는 거버넌스 시스템을 개선하기 위해 여러 차례 하드포크를 거쳤습니다. 이러한 변화는 네트워크와 이해관계자의 변화하는 요구를 반영합니다.

## KIP-163: 공개 위임이 있는 CnStakingV3 <a id="KIP-163"></a>

**날짜: 2024-04-30**

KIP-163은 퍼블릭 위임을 지원하는 새로운 CnStakingV3를 도입하여 일반 사용자가 KAIA 토큰을 더 쉽게 위임하고 재위임할 수 있도록 합니다. 스테이킹 정보 간격도 86,400블록에서 1블록으로 변경되어 검증자 세트와 보상 분배의 효율성과 정확성이 향상되었습니다.
이번 업데이트의 동기는 소수의 검증인만 공개 위임 서비스를 제공하여 사용자의 선택권이 제한되었던 CnStakingV2의 한계를 해결하기 위함입니다. CnStakingV3에서 기본적으로 공개 위임을 활성화함으로써 사용자는 이 서비스를 제공하는 모든 검증자에게 KAIA를 위임할 수 있으므로 위임 옵션이 넓어지고 네트워크 참여가 향상됩니다.
CnStakingV3는 주소록 및 스테이킹트랙커와 같은 기존 핵심 컨트랙트와 호환됩니다. 공개 위임은 이자지급형 토큰 모델(ERC-4626)을 기반으로 하는 새로운 계약인 PublicDelegation을 통해 이루어지며, 사용자는 자신의 자산을 나타내는 양도 불가능한 주식을 받게 됩니다. 이 접근 방식은 모든 사용자가 위임 프로세스를 보다 투명하고 쉽게 이용할 수 있도록 하는 것을 목표로 합니다.
또한, 포크 블록 이후부터는 블록 N에 대한 스테이킹 정보가 블록 N-1에서 파생되어 스테이킹 프로세스에서 보다 시기적절하고 정확한 업데이트를 보장합니다. 이번 변경은 카이아 네트워크에서 스테이킹과 위임의 역동적인 특성을 지원하여 더욱 강력하고 사용자 친화적으로 만들기 위해 고안되었습니다.

## KIP-160: 국고 펀드 리밸런싱에 대한 업데이트 <a id="KIP-160"></a>

**날짜: 2024-04-22**

KIP-160은 KIP-103의 한계를 해결하기 위해 국고금 재조정 메커니즘의 개선을 제안합니다. 펀드 잔액의 증가와 감소를 모두 허용하고 재조정 블록 번호를 편집할 수 있도록 하는 것이 주요 동기입니다. 이번 업데이트에는 리밸런싱 프로세스를 일반화하여 자금 소각과 발행을 모두 가능하게 하고, 블록체인의 하드포크 일정 변경에 따라 리밸런싱 블록 번호를 조정할 수 있도록 하는 내용이 포함되어 있습니다. 계약 주소](../../references/contract-addresses.md) 페이지에서 재조정 세부 정보를 확인하세요.

## KIP-149: 시스템 계약의 통합 배포 및 관리 <a id="KIP-149"></a>

**날짜: 2023-09-20**

KIP-149는 카이아 블록체인에서 시스템 컨트랙트를 배포하고 관리하는 표준화된 방법을 소개합니다. 시스템 계약은 카이아 코어 프로토콜에 직접 영향을 미치거나 읽혀집니다. 이 제안에는 이러한 계약을 효율적으로 관리하기 위한 중앙 집중식 레지스트리 계약이 포함되어 있습니다. 현재 시스템 계약의 배포 방식은 표준화가 부족하여 비효율성을 초래하고 있습니다. KIP-149는 다음과 같은 주요 기능을 도입하여 이 문제를 해결합니다:

- 레지스트리 계약: 모든 시스템 계약을 관리하여 거버넌스 계약을 통해 중앙 집중식 추적 및 거버넌스 제어를 보장합니다.
- 프록시 패턴: UUPS 프록시 패턴을 사용하여 데이터 저장소와 로직을 분리하여 저장된 데이터에 영향을 주지 않고 업데이트를 허용하고 업그레이드 가능성을 보장합니다.
 시스템 계약 수명 주기: 계약을 효율적으로 관리하기 위한 단계(등록, 활성, 사용 중단)를 정의합니다.
- 거버넌스 및 업그레이드: 새로운 계약 또는 업그레이드를 위한 커뮤니티 중심의 제안 및 투표 프로세스를 구축하여 분산된 제어와 원활한 구현을 보장합니다.
 KIP-149는 시스템 계약 관리에 대한 안전하고 확장 가능한 접근 방식을 제공하고, 보다 원활한 업그레이드를 촉진하며, 분산 제어를 촉진함으로써 Kaia의 거버넌스와 운영 효율성을 향상하는 것을 목표로 합니다.

## KIP-103: 국고 펀드 리밸런싱 <a id="KIP-103"></a>

**날짜: 2023-04-06**

KIP-103은 v1.10.2 릴리스에서 도입되었습니다. KGP-6의 실행을 위한 제안인 국고금 재조정 메커니즘을 소개합니다.

KGP-6 제안은 지속 가능하고 검증 가능한 KAIA 토큰 이코노미를 구축하는 것을 목표로 합니다. 거버넌스 위원회는 31명의 위원 중 26명이 찬성표를 던져 이 안건을 승인했습니다. 이 제안에는 1inch, Swapscanner, Ground X 등 다양한 단체의 기여가 포함되어 있습니다. 목표는 KAIA 토큰을 위한 더욱 견고하고 투명한 생태계를 만드는 것입니다. 계약 주소](../../references/contract-addresses) 페이지에서 재조정 세부 정보를 확인하세요.

## KIP-82: KIP-82: 지니 계수 폐지로 인한 새로운 GC 보상 구조 <a id="KIP-82"></a>

**날짜: 2022-09-21**

v1.10.0에서 도입되어 코레 하드포크에서 활성화된 KIP-82는 블록 제안자 선택과 보상에 사용되는 지니 계수를 제거하여 카이아의 이전 보상 시스템의 한계를 해결합니다. 이러한 변화는 기존 기업과 함께 DAO를 포함하는 거버넌스 위원회의 확장을 지원하며, 보다 균형 잡히고 포용적인 보상 체계가 필요해졌습니다.
주요 변경 사항으로는 새로운 보상 분배 구조가 있으며, GC 보상의 20%는 블록 제안자에게, 80%는 스테이커에게 돌아갑니다. 이는 두 그룹 모두에게 공정한 보상을 보장하여 네트워크 안정성과 참여도를 높입니다. 이제 투표권과 보상이 스테이킹된 KAIA 토큰의 양에 직접적으로 연동되어, 더 많은 네트워크 참여를 장려하고 소수의 고지분 노드에 대한 의존도를 낮출 수 있습니다. 블록 제안자는 스테이킹 금액과 무관하게 동등한 기회에 따라 선정되며, 프로세스를 민주화하고 탈중앙화를 강화합니다.
스테이킹의 기회비용을 보상하기 위해 인플레이션 보상이 도입되어 일반 사용자들이 GC가 관리하는 스테이킹 프로그램에 참여하고 보상을 받을 수 있어 네트워크 성장과 안정성을 촉진할 수 있습니다.
새로운 거버넌스 매개변수(reward.kip82ratio)는 제안자와 스테이킹 보상 간의 초기 보상 분배 비율을 20/80으로 설정하며, 거버넌스 프로세스를 통해 조정할 수 있습니다.
KIP-82는 네트워크 기여에 대한 공정한 보상을 보장하고 보다 탈중앙화되고 탄력적인 네트워크를 육성하는 카이아의 보다 공평하고 안정적인 거버넌스 시스템을 향한 중요한 발걸음을 내디뎠습니다.

## KIP-81: 온체인 거버넌스 투표 방식 구현 <a id="KIP-81"></a>

**날짜: 2022-09-19**

KIP-81은 v1.10.0에서 도입되었으며 코레 하드포크에서 활성화되었습니다. 거버넌스 포털을 통해 지분 기반 투표 메커니즘을 도입했습니다. 이 접근 방식은 각 거버넌스 카운슬(GC) 멤버가 한 표를 행사하는 현행 시스템에서, 한 개 주체가 과도한 영향력을 행사하지 못하도록 상한선을 두고 지분 보유량에 따라 의결권이 결정되는 모델로 전환하는 것입니다.
GC 멤버는 일정량의 스테이킹된 KAIA(초기에는 500만 KAIA로 설정)당 1표의 투표권을 가지며, 총 GC 멤버 수보다 1표 적은 한도를 갖습니다. 예를 들어, GC 멤버가 35명인 경우 최대 투표권은 34표로 제한되어 독점을 방지할 수 있습니다.
이 시스템을 통해 거버넌스 주제는 거버넌스 포럼에서 논의되고 클레이튼 스퀘어에서 투표를 통해 결정되며, 의사 결정의 책임성과 투명성을 보장합니다.
이번 변경은 투표 절차를 더욱 투명하게 만들고 카이아에 가장 많이 투자한 사람들의 이해관계를 반영하기 위한 것입니다. 이 시스템은 투표권을 스테이킹 금액에 연동함으로써 더 많은 참여와 이해관계의 조정을 장려하여 생태계에 더 많이 기여하는 사람들이 결정에 비례하여 영향력을 행사할 수 있도록 합니다.

이러한 거버넌스 구조는 공정하고 균형 잡힌 의사 결정 과정을 보장하여 투표권을 생태계에 대한 기여도에 맞추는 동시에 특정 주체가 과도한 영향력을 행사하는 것을 방지합니다.
