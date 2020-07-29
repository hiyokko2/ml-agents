# インストール

ML-Agents ツールキットはいくつかのコンポーネントを含みます。

- Unity パッケージ([`com.unity.ml-agents`](../com.unity.ml-agents/))には Unity シーンに統合される C# SDK が含まれています。
- 3 つの Python パッケージ
  - [`mlagents`](../ml-agents/)には Unity シーンの動作を訓練できる機械学習アルゴリズムが含まれています。ほとんどの ML-Agents ユーザーは`mlagents`を直接インストールするだけで済みます。
  - [`mlagents_envs`](../ml-agents-envs/)は Unity シーンと通信するための Python API を含みます。これは Unity シーンと Python 機械学習アルゴリズムの間のメッセージのやりとりを容易にする基本的なレイヤーです。
    したがって、`mlagents`は`mlagents_envs`に依存します。
  - [`gym_unity`](../gym-unity/)は OpenAI Gym インターフェースをサポートする Unity シーン用の Python ラッパーを提供します。
- ツールキットのさまざまな機能を強調するいくつかの[環境例](Learning-Environment-Examples.md)を含む Unity [プロジェクト](../Project/)

したがって、ML-Agents をインストールして使用するには以下のことをする必要があります。

- Unity のインストール(バージョン 2018.4 以降)
- Python のインストール(バージョン 3.6.1 以降)
- このリポジトリのクローン(任意)
  - **注意：** このリポジトリのクローンを作成しないとサンプル環境と訓練設定のサンプルにアクセスできません。
    さらに、[入門ガイド](Getting-Started.md)は、あなたがこのリポジトリをクローンしたという前提で進みます。
- `com.unity.ml-agents`Unity パッケージのインストール
- `mlagents` Python パッケージのインストール

### **Unity(バージョン 2018.4 以降)**のインストール

Unity を[ダウンロード](https://unity3d.com/get-unity/download)してインストールしてください。
Unity Hub を使用して Unity をインストールすることを強くお勧めします。Unity Hub は複数のバージョンの Unity を管理することが出来るからです。

### **Python(バージョン 3.6.1 以降)**のインストール

Python のバージョン 3.6 か 3.7 を[インストール](https://www.python.org/downloads/)することをお勧めします。
もし Windows を使用しているなら、x86 バージョンではなく x86-64 バージョンをインストールしてください。
もし、あなたの Python 環境に`pip3`が入っていない場合は[インストール手順](https://packaging.python.org/guides/installing-using-linux-tools/#installing-pip-setuptools-wheel-with-linux-package-managers)を参考にしてください。

Windows での Anaconda インストールはサポートしていませんが、以前の[Windows Anaconda インストール (非推奨) ガイド](Installation-Anaconda-Windows.md)は引き続き利用できます。

### (任意)ML-Agents ツールキットリポジトリのクローン

Unity と Python をインストールしたので Unity と Python のパッケージをインストールできます。
翻訳中・・・

```sh
git clone --branch release_4 https://github.com/Unity-Technologies/ml-agents.git
```

`--branch release_4`オプションは最新の安定リリースのタグに切り替えます。
これを省略すると不安定になる可能性のある`master`ブランチが取得されます。

#### 翻訳中・・・

### `com.unity.ml-agents`Unity パッケージのインストール

Unity ML-Agents C# SDK は Unity のパッケージです。`com.unity.ml-agents`パッケージは[パッケージマネージャーレジストリ](https://docs.unity3d.com/Manual/upm-ui-install.html)から直接インストールできます。
このパッケージを見つけるために Advanced ドロップダウンの Preview Packages を有効にするのを忘れないでください。

翻訳中・・・
