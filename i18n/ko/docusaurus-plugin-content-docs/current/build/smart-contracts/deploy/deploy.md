# 스마트 컨트랙트 배포

카이아에 스마트 컨트랙트를 배포하는 방법에는 여러 가지가 있습니다. 이 문서는 다양한 도구를 사용하여 샘플 컨트랙트를 배포하는 단계별 가이드를 제공합니다. 트랜잭션 수수료를 지불할 수 있는 충분한 KAIA를 보유한 카이아 계정이 있다고 가정합니다. 계정을 만들려면 [카이아 툴킷](https://toolkit.kaia.io/account/)을 사용할 수 있습니다."

## Remix 온라인 IDE <a id="remix-ide"></a>

Open your internet browser and go to [Kaia Plugin for Remix](https://ide.kaia.io).

1. 새 파일을 추가합니다.

![](/img/build/smart-contracts/01_deployment_ide.png)

2. 새 파일에 다음 샘플 코드(또는 배포하려는 코드)를 복사하여 붙여넣습니다. 이 코드는 Mortal과 KaiaGreeter라는 두 개의 컨트랙트로 구성되어 있으며, 간단한 "Hello World!"를 실행할 수 있습니다.

```
pragma solidity 0.5.12;

contract Mortal {
    /* Define variable owner of the type address */
    address payable owner;
    /* This function is executed at initialization and sets the owner of the contract */
    constructor () public { owner = msg.sender; }
    /* Function to recover the funds on the contract */
    function kill() public payable { if (msg.sender == owner) selfdestruct(owner); }
}

contract KaiaGreeter is Mortal {
    /* Define variable greeting of the type string */
    string greeting;
    /* This runs when the contract is executed */
    constructor (string memory _greeting) public {
        greeting = _greeting;
    }
    /* Main function */
    function greet() public view returns (string memory) {
        return greeting;
    }
}
```

3. 아이콘 패널에서 컴파일러를 선택합니다. 원하는 EVM 환경을 선택합니다. Kaia 네트워크의 경우 Kairos(테스트넷)과 메인넷 중에서 선택할 수 있습니다. 실제 배포하기 전에 샘플 코드를 준수할 준비가 되면 `Compile`을 클릭합니다.

![](/img/build/smart-contracts/02_deployment_compile.png)

4. 이제 컨트랙트를 배포할 수 있습니다. 아이콘 패널에서 카이아 로고를 클릭합니다. `Account` 옆의 더하기 버튼을 클릭하여 계정을 가져옵니다. 계정에 필요한 스마트 컨트랙트 배포 트랜잭션 비용을 지불할 수 있는 충분한 KAIA가 있는지 확인합니다.

![](/img/build/smart-contracts/05_deployment_account.png)

5. 가스 한도 및 전송할 값을 설정합니다.

- 더 복잡한 컨트랙트를 배포하는 경우 가스 한도를 더 높게 설정해야 할 수도 있습니다. 이 예제에서는 그대로 두셔도 됩니다.
- 배포 시점에 컨트랙트에 `KLAY`를 보내지 않으려면 `Value`를 0으로 설정합니다.

6. 생성자 함수의 인자로 "Hello World!"를 입력하고 `Deploy` 버튼을 클릭합니다.

![](/img/build/smart-contracts/03_deployment_hello.png)

7. 컨트랙트가 성공적으로 배포되면 터미널에서 해당 트랜잭션 영수증과 상세 결과를 확인할 수 있습니다.

8. 기능 버튼을 클릭하여 컨트랙트와 상호작용할 수 있습니다. 함수는 다른 색상으로 표시됩니다. Solidity의 `constant` 또는 `pure` 함수는 파란색 버튼(예시에서는 `greet`)을 가지며 새 트랜잭션을 생성하지 않으므로 가스 비용이 들지 않습니다. 빨간색 버튼(예시에서는 `kill`)은 블록체인의 상태를 변경하고 가스를 소비하며 가치를 받을 수 있는 `payable` 함수를 나타냅니다. 주황색 버튼은 컨트랙트 상태를 변경하지만 값을 받지 않는 \`non-payable\`\\` 함수를 나타냅니다.

![](/img/build/smart-contracts/06_deployment_functions.png)

자세한 내용은 이 [링크](../ide-and-tools/ide-and-tools.md)를 참조하세요.

## VVISP <a id="vvisp"></a>

vvisp는 헤이치랩스에서 제공하는 스마트 컨트랙트 개발을 위한 사용하기 쉬운 CLI 도구/프레임워크입니다. 명령어 하나로 카이아 스마트 컨트랙트의 환경 설정, 배포, 실행을 쉽게 할 수 있습니다. 자세한 내용은 아래 링크를 참고하세요.

- https://henesis.gitbook.io/vvisp/deploying-smart-contracts