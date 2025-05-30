# 실행 모델

이 페이지는 카이아 스마트 컨트랙트의 실행 모델, 데이터 구조, 생명주기에 대해 설명합니다.

## 실행 모델 <a id="execution-model"></a>

트랜잭션은 [플랫폼 API 사양](../../../references/json-rpc/kaia/account-created)에 설명된 대로 플랫폼 API를 통해 생성할 수 있습니다. 이러한 트랜잭션은 _컨센서스 노드(CN)_ 로 전송되어 블록에 저장됩니다. CN은 수신된 각 트랜잭션이 유효한지 확인합니다. 유효한 트랜잭션은 트랜잭션 풀에 저장되며, 그렇지 않은 트랜잭션은 폐기됩니다. CN은 트랜잭션 풀에서 현재 블록에 있는 실행 가능한 트랜잭션을 선택하고 하나씩 실행합니다.

트랜잭션을 실행하려면 발신자는 트랜잭션 수수료로 일정 금액의 KAIA를 지불해야 합니다. KAIA의 트랜잭션 수수료는 가스와 승수, 즉 단가를 기준으로 계산됩니다. 가스는 계산의 기본 단위입니다. Kaia 노드에서 실행되는 모든 작업은 미리 정의된 양의 가스를 소비합니다. The exact amount of KAIA required for the transaction is calculated by the formula illustrated in [Transaction Fees](../transaction-fees/transaction-fees.md). 발신자가 가스 부족과 함께 트랜잭션을 제출하면 트랜잭션이 실패할 수 있습니다. 또한 발신자 계정의 잔액이 부족한 경우에도 트랜잭션이 실패할 수 있습니다.

트랜잭션이 성공적으로 실행되면 해당 트랜잭션은 현재 블록에 포함됩니다. CN은 블록 가스 한도 또는 블록 시간 제한에 도달할 때까지 트랜잭션을 수집합니다. 그런 다음 CN은 트랜잭션으로 블록을 생성합니다. 이 단계에서는 블록의 여러 필드를 채워야 합니다. 예를 들어 트랜잭션, 영수증, 상태 등의 해시값을 계산해야 합니다. 모든 필수 필드가 완료되면 CN은 블록 해시를 생성합니다.

블록 생성이 완료되면 블록은 다른 모든 CN에 전파됩니다. 다른 CN들은 모두 전파된 블록을 검증하고 BFT 합의 알고리즘을 사용하여 검증 결과에 대한 합의에 도달합니다. 대다수의 CN에 의해 검증 프로세스가 성공적으로 완료되면 블록이 블록체인에 저장됩니다. BFT 합의 알고리즘은 즉각적인 완결성 속성을 만족하기 때문에 블록은 최종적이며 절대 제거되지 않습니다. 블록이 확정된 후에는 해당 블록에 포함된 모든 트랜잭션의 실행이 비가역적으로 보장되며, 요청 시 발신자에게 트랜잭션 실행 결과를 반환할 수 있습니다.

## Enhanced Randomness in Block Proposer and Committee Selection <a id="enhanced-randomness-in-block-proposer-and-committee-selection"></a>

Kaia has implemented a new mechanism to introduce verifiable on-chain randomness in the block proposer and committee selection processes. This mechanism involves two new fields in the block header: `randomReveal` and `mixHash`.

In this system, block proposers generate and commit to random values. The `randomReveal` field in a block contains the proposer's signature, generated using a specific signature scheme, and is calculated based on the current block number being proposed. The `mixHash` is then computed using this revealed random value along with other block data, creating a source of randomness for the network.

The block proposer and committee selection processes utilize this generated randomness. The use of this randomness aims to make the selection processes more unpredictable and fair, enhancing the overall security of the network. One particular use case for this mechanism is allowing block proposers to remain private until the previous block is completed, adding an extra layer of security to the network.

The execution flow creates a cycle where each block's randomness influences future block proposer and committee selections. This introduces an element of unpredictability to these processes while maintaining their verifiability.

It's important to note that while this randomness is used in selection processes, rewards are still distributed at the end of block mining by directly modifying states, based on staking amounts. The randomness determines which validators are selected to be part of the committee that receives rewards, not the amount of rewards distributed.

Several security considerations are crucial to this mechanism:

- To prevent replay attacks, each `randomReveal` value must be unique for each block.
- Block proposers must honestly generate and submit their `randomReveal` to prevent manipulation of the `mixHash`.
- Proposers must keep their `randomReveal` secret until the block proposal to prevent prediction and potential manipulation by other participants.
- The `randomReveal` must be properly signed and validated to prevent tampering.

This mechanism aims to introduce unpredictability in the block creation and committee selection processes while maintaining verifiability. It's important to note that while this system provides a framework for enhanced randomness, the specific implementations of proposer and committee selection algorithms using this randomness may evolve over time as the network develops and improves.

### 트랜잭션 실행 제한 <a id="restrictions-on-transaction-execution"></a>

Kaia Mainnet and Kairos Testnet currently have the following restrictions on the transaction execution:

- You can set gasPrice of the transaction, but it means it's the most you can pay. The actual gasPrice will be determined by network. For more detailed information, see [gas price overview](../transaction-fees/transaction-fees.md#gas-price-overview)
- A block proposer shall not spend more than 250 ms in block execution. [계산 비용](./computation-cost.md)을 참조하세요.
- 트랜잭션은 계산 비용 한도를 초과하여 사용할 수 없습니다. 계산 비용](./computation-cost.md)을 참조하세요.

### Restrictions on Smart Contract Deployment <a id="restriction-on-smart-contract-deployment"></a>

Kaia implements several restrictions on smart contract deployment:

- As of the Kore hardfork, deployment of new contract code starting with the 0xEF byte is not allowed, as per [EIP-3541](https://eips.ethereum.org/EIPS/eip-3541).
- Since the Shanghai hardfork:
  - Deployment of new contract code is rejected if the initcode length exceeds 49,152 bytes.
  - The length of the new contract code cannot exceed 24,576 bytes.
  - These limitations are based on [EIP-3860](https://eips.ethereum.org/EIPS/eip-3860).
  - Smart contract account (SCA) overwriting over externally owned accounts (EOA) is enabled.

## 데이터 구조 <a id="data-structures"></a>

### 계정(Account) <a id="account"></a>

카이아 블록체인 플랫폼에서 계정은 개인의 잔액이나 스마트 컨트랙트에 대한 정보를 담고 있는 데이터 구조입니다. 카이아는 더 나은 DX와 UX를 제공하기 위해 계정 모델을 재설계합니다. 계정 모델에 대한 자세한 정보는 [여기](../accounts.md)에서 확인할 수 있습니다.

### 트랜잭션(Transaction) <a id="transaction"></a>

블록체인 플랫폼에서 트랜잭션은 노드 간에 전송되는 메시지로, 블록체인의 상태를 변경합니다. 카이아는 트랜잭션 모델도 새롭게 디자인했습니다. 트랜잭션을 목적에 따라 다양한 유형으로 구분하여 성능 최적화의 기회를 찾고, 재설계된 계정 모델을 지원합니다. 거래 모델에 대한 자세한 정보는 [여기](../../build/transactions/transactions.md)에서 확인할 수 있습니다.

### 상태(State) <a id="state"></a>

카이아의 **State**는 계정 상태의 집합입니다. 이 상태는 동일한 블록을 동일한 순서로 처리한 경우 Kaia 노드에서 동일해야 합니다. 상태는 트랜잭션이 카이아 노드에서 실행될 때 변경됩니다.

아래 표는 주에 저장된 계정 데이터를 보여줍니다.

| 구성 요소       | 설명                                                                                                                                                 |
| :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------- |
| nonce       | 이 계정에서 실행한 트랜잭션의 수를 나타내는 정수 값입니다. 트랜잭션을 제출할 때 트랜잭션의 nonce는 계정의 nonce와 같아야 합니다.                                     |
| balance     | 이 계정이 현재 보유하고 있는 KAIA의 양을 나타내는 정수 값입니다.                                                                                            |
| storageRoot | 계정의 모든 저장소 변수 값을 포함하는 머클 패트리샤 트리의 루트에 대한 256비트 해시입니다.                                                                              |
| codeHash    | 계정의 바이트코드 해시입니다.  이 값은 변경할 수 없으므로 스마트 컨트랙트가 생성될 때만 설정됩니다.  계정이 EOA 또는 EA인 경우 이 값은 null의 해시로 설정됩니다. |

### 블록 <a id="block"></a>

블록체인은 말 그대로 블록의 체인으로 구성되어 있기 때문에 블록은 Kaia 블록체인의 중요한 요소입니다. 아래 표는 블록의 구성 요소를 보여줍니다.

| 구성 요소            | 설명                                                                                                          |
| :--------------- | :---------------------------------------------------------------------------------------------------------- |
| baseFeePerGas    | 가스당 기본 수수료입니다. 이 값은 해당 블록 번호에 대해 EthTxTypeCompatibleBlock이 활성화된 경우에만 반환됩니다. |
| blockScore       | Former difficulty. 이전 난이도 BFT 합의 엔진에서는 항상 1입니다.                             |
| extraData        | 이 블록의 "추가 데이터" 필드입니다.                                                                       |
| gasUsed          | 이 블록의 모든 트랜잭션이 사용한 총 가스 사용량입니다.                                                             |
| governanceData   | RLP로 인코딩된 거버넌스 구성                                                                                           |
| logsBloom        | 블록의 로그에 대한 블룸 필터입니다. 보류 중인 블록인 경우 `null`입니다.                                |
| number           | 블록 번호. 보류 중인 블록인 경우 `null`.                                                 |
| parentHash       | 블록의 부모 블록의 해시입니다.                                                                           |
| proposer         | 블록 제안자의 주소입니다.                                                                              |
| receiptsRoot     | 블록의 영수증 시도 루트입니다.                                                                           |
| reward           | 블록 보상을 받는 주소입니다.                                                                            |
| size             | 이 블록의 크기(바이트)를 정수로 표시합니다.                                                |
| stateRoot        | 블록의 최종 상태 트라이의 루트입니다.                                                                       |
| totalBlockScore  | 이 블록까지 체인의 총 블록점수의 정수입니다.                                                                   |
| transactionsRoot | 블록의 트랜잭션 트라이의 루트입니다.                                                                        |
| timestamp        | 블록이 콜레이트된 시점의 유닉스 타임스탬프입니다.                                                                 |
| timestampFoS     | 블록이 콜레이트된 시점에 대한 타임스탬프의 1초 단위입니다.                                                           |
| transactions     | 트랜잭션 객체의 배열 또는 마지막 매개변수에 따라 32바이트 트랜잭션 해시입니다.                                               |
| voteData         | 제안자의 RLP 인코딩된 거버넌스 투표                                                                                       |

## 스마트 컨트랙트 수명 주기 <a id="smart-contract-lifecycle"></a>

스마트 컨트랙트는 카이아 블록체인의 특정 주소에 있는 코드 (functions)와 데이터 (state)의 모음으로 구성됩니다. 컨트랙트 계정은 서로 메시지를 전달할 수 있을 뿐만 아니라 사실상 튜링 완전 연산을 수행할 수 있습니다. Contracts exist on the blockchain in Kaia-specific binary formats. 현재 카이아는 이더리움 가상머신 (EVM) 바이트코드 한 가지 바이너리 형식을 지원하지만, 향후 다른 형식도 지원될 예정입니다.

### 스마트 컨트랙트 만들기 <a id="creating-smart-contracts"></a>

스마트 컨트랙트는 바이너리를 데이터로 빈 주소로 트랜잭션을 전송하여 Kaia 블록체인에서 생성할 수 있습니다. 바이너리는 다양한 형식이 있을 수 있지만, 현재 카이아는 EVM 바이트코드라는 한 가지 바이너리 형식을 지원합니다. 이 트랜잭션은 실행을 위해 지불이 필요하다는 점을 지적할 필요가 있습니다. 트랜잭션이 블록에 저장된 후 트랜잭션 수수료 모델에 따라 발신자 계정의 계정 잔액이 감소합니다. 일정 시간이 지나면 트랜잭션이 블록에 표시되며, 이는 트랜잭션이 수반하는 상태가 합의에 도달했음을 확인합니다. 이 시점에서 스마트 컨트랙트는 이제 카이아 블록체인에 존재합니다.

### 스마트 컨트랙트 실행하기 <a id="executing-smart-contracts"></a>

스마트 컨트랙트의 함수는 스마트 컨트랙트에 트랜잭션을 보내거나 노드에서 로컬로 함수를 호출하여 호출하고 실행할 수 있습니다. 트랜잭션을 전송하여 함수를 호출하면 트랜잭션을 처리하여 함수가 실행됩니다. 여기에는 트랜잭션 전송에 대한 KAIA 비용이 수반되며, 호출은 블록체인에 영구적으로 기록됩니다. 이러한 방식으로 호출된 함수의 반환값은 트랜잭션의 해시입니다. When the function is invoked locally, it is executed locally in the Kaia Virtual Machine \(KVM\), and the call returns the return value of the function. 이러한 방식으로 호출된 함수는 블록체인에 기록되지 않으므로 컨트랙트의 내부 상태를 수정할 수 없습니다. 이러한 유형의 호출을 상수 함수 호출이라고 합니다. 이러한 방식으로 호출하면 KAIA 비용이 발생하지 않습니다. 상수 함수 호출은 리턴 값에만 관심이 있을 때 사용해야 하며, 트랜잭션은 컨트랙트 상태에 대한 부작용에 관심이 있을 때 사용해야 합니다.

When executing a smart contract function through a transaction, gas costs are incurred. The exact gas cost depends on the operations performed by the function and is calculated based on predefined gas costs for each EVM operation.

### 스마트 컨트랙트 비활성화하기 <a id="disabling-smart-contracts"></a>

스마트 컨트랙트는 카이아 블록체인에 존재하기 때문에 삭제할 수 없으며 비활성화할 수만 있습니다. 현재 카이아는 이더리움에서 스마트 컨트랙트를 비활성화하는 데 사용되는 것과 동일한 프로세스를 카이아 스마트 컨트랙트를 비활성화하는 데 채택했습니다. 예를 들어, Solidity에서 [`selfdestruct(주소 수신자)`](https://solidity.readthedocs.io/en/v0.5.6/introduction-to-smart-contracts.html#self-destruct) 호출(또는 KLVM 연산자 코드 `SELFDESTRUCT`)을 사용하여 KLVM용 Kaia 스마트 컨트랙트를 비활성화할 수 있습니다. 카이아 팀은 다른 실행 환경에서도 스마트 컨트랙트를 비활성화하는 방법을 제공할 예정입니다.

### 스마트 컨트랙트 업그레이드하기 <a id="upgrading-smart-contracts"></a>

카이아는 기존 블록체인의 불편한 사용자 경험을 해결하기 위해 배포된 스마트 컨트랙트를 업그레이드할 수 있는 방법을 제공할 것입니다. 예를 들어 블록체인에 배포된 서비스는 업그레이드가 어렵습니다. 카이아는 서비스 공급자 (SP)가 배포된 서비스를 업그레이드하고 서비스 정보를 마이그레이션할 수 있도록 프레임워크와 스마트 컨트랙트 라이브러리를 제공할 것입니다. 카이아는 다음 요구사항을 고려하여 이 기능을 신중하게 제공할 것입니다.

- 승인된 계정 또는 스마트 컨트랙트 소유자만 스마트 컨트랙트를 업그레이드할 수 있어야 합니다.
- 업그레이드된 스마트 컨트랙트는 이전 스마트 컨트랙트가 유지 관리하는 기존 데이터를 조작할 수 있어야 합니다.
- 이전 스마트 컨트랙트를 참조하는 다른 스마트 컨트랙트는 해당 스마트 컨트랙트의 업그레이드된 최신 버전을 사용할지 여부를 결정할 수 있어야 합니다.
