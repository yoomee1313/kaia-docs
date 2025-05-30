# スマートコントラクトのデプロイ

スマート・コントラクトをカイアにデプロイするには、さまざまな方法がある。 この文書では、さまざまなツールを使用してサンプル契約を展開するためのステップバイステップのガイドを提供します。 取引手数料を支払うのに十分なKAIAアカウントをお持ちであることを前提としています。 アカウントを作成するには、[Kaia Toolkit](https://toolkit.kaia.io/account/)をご利用ください。"

## リミックス・オンラインIDE<a id="remix-ide"></a>

インターネットブラウザを開き、[Kaia Plugin for Remix](https://ide.kaia.io)にアクセスします。

1. 新しいファイルを追加する。

![](/img/build/smart-contracts/01_deployment_ide.png)

2. 以下のサンプルコード（または配置したいコード）をコピーして、新しいファイルに貼り付けます。 このコードはMortalとKaiaGreeterと呼ばれる2つのコントラクトで構成されており、シンプルな "Hello World!"を実行することができる。

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

3. アイコンパネルでコンパイラを選択します。 必要なEVM環境を選択します。 Kaiaネットワークでは、Kairos（テストネット）とMainnetのいずれかを選択できます。 実際のデプロイ前にサンプルコードをコンパイルする準備ができたら、`Compile`をクリックする。

![](/img/build/smart-contracts/02_deployment_compile.png)

4. これで契約を展開できる。 アイコンパネルのKaiaロゴをクリックします。 アカウント`の横にあるプラスボタンをクリックしてアカウントをインポートします。 これで契約を展開できる。 アイコンパネルのKaiaロゴをクリックします。 アカウント\`の横にあるプラスボタンをクリックしてアカウントをインポートします。

![](/img/build/smart-contracts/05_deployment_account.png)

5. ガスリミットと送信する値を設定します。

- より複雑な契約を展開する場合は、ガス上限を高く設定する必要があるかもしれません。 この例では、そのままでいい。 この例では、そのままのままにしてください。
- デプロイ時にコントラクトに `KAIA` を送信したくない場合は `Value` を 0 に設定する。

6. コンストラクタ関数の引数に "Hello World!"を入力し、`Deploy`ボタンをクリックする。

![](/img/build/smart-contracts/03_deployment_hello.png)

7. コントラクトが正常にデプロイされると、対応するトランザクションのレシートと詳細な結果がターミナルに表示されます。

8. 機能ボタンをクリックすることで、契約を操作することができます。 各機能は異なる色で表現されている。 Solidityの `constant` または `pure` 関数はブルーボトルを持ち（例では `greet`）、新しいトランザクションを作成しないので、ガス代を消費しません。 赤いボタン（例では`kill`）は、ブロックチェーン上の状態を変更し、ガスを消費し、価値を受け入れることができる`支払い可能な`機能を表している。 オレンジ色のボタンは、契約状態を変更するが、値を受け付けない`non-payable`関数用です。

![](/img/build/smart-contracts/06_deployment_functions.png)

詳しくはこちらの[リンク](../ide-and-tools/ide-and-tools.md)をご参照ください。

## VVISP <a id="vvisp"></a>

vvispは、HEACHI LABSが提供する、スマートコントラクトを開発するための使いやすいCLIツール／フレームワークです。 Kaiaスマートコントラクトの環境設定、デプロイ、実行は1つのコマンドで簡単に行える。 詳細は以下のリンクを参照。 Kaiaスマートコントラクトの環境設定、デプロイ、実行は1つのコマンドで簡単に行える。 詳細は以下のリンクを参照。

- https://henesis.gitbook.io/vvisp/deploying-smart-contracts