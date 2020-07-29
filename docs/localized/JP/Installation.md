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
