# 新しい学習環境を作る

このチュートリアルでは一から Unity の学習環境を構築する方法を説明します。
まず最初に[入門ガイド](Getting-Started.md)を読んで、ここで説明される概念を構築済みの環境で学んでおくことをお勧めします。

![A simple ML-Agents environment](../../images/mlagents-NewTutSplash.png)

この例では、プラットフォーム上のボールを制御できるエージェントを作成します。そしてボールが落ちないようにキューブに向かって転がすエージェントを訓練します。

## 概要

Unity プロジェクトの ML-Agents ツールキットを使うためには、次のステップを踏む必要があります。

1. エージェントが存在する環境を作成します。環境はいくつかのオブジェクトを含むシンプルな物理シミュレーションから、ゲーム全体やエコシステムまでさまざまです。

1. エージェントのサブクラスを実装します。エージェントのサブクラスは、環境を観察する、割り当てられた行動を実行する、強化学習のための報酬を計算するコードを定義します。追加のメソッドを実装してタスクが終了もしくは失敗した場合にリセットすることもできます。

1. 作成したエージェントのサブクラスを適切なゲームオブジェクトに追加します。通常、シミュレーションでエージェントを表すオブジェクトに追加します。

翻訳中・・・

## Unity プロジェクトを設定する

新しく Unity プロジェクトを作成し、ML-Agents アセットをインポートしましょう。

1. Unity Hub を起動し RollerBall という名前の 3D プロジェクトを作成します
1. プロジェクトに[ML-Agents Unity パッケージを追加します](Installation.md#install-the-comunityml-agents-unity-package)

Unity プロジェクトウィンドウは下の画像のようになるはずです。

<p align="left">
  <img src="../../images/roller-ball-projects.png"
       alt="Project window"
       width="250" border="10" />
</p>

## 環境を作成する

次に、学習環境として機能するシンプルなシーンを作成します。環境の物理コンポーネントは、エージェントが動き回るための床、ゴールとしてのキューブ、動き回るエージェントとしての球です。

### 床を作成する

1. ヒエラルキーウィンドウを右クリックし、3D オブジェクト > 平面を選択します。
1. 作成した平面の名前を Floor にします。
1. Floor を選択し、インスペクタウィンドウを表示します。
1. トランスフォームの位置を`(0, 0, 0)`、回転を`(0, 0, 0)`、拡大/縮小を`(1, 1, 1)`に設定します。

<p align="left">
  <img src="../../images/roller-ball-floor.png"
       alt="The Floor in the Inspector window"
       width="400" border="10" />
</p>

### ゴールとしてのキューブを設置する

1. ヒエラルキーウィンドウを右クリックし、3D オブジェクト > キューブを選択します。
1. 作成したキューブの名前を Target にします。
1. Target を選択し、インスペクタウィンドウを表示します。
1. トランスフォームの位置を`(3, 0.5, 3)`、回転を`(0, 0, 0)`、拡大/縮小を`(1, 1, 1)`に設定します。

<p align="left">
  <img src="../../images/roller-ball-target.png"
       alt="The Target Cube in the Inspector window"
       width="400" border="10" />
</p>

### エージェントとしての球の追加

1. ヒエラルキーウィンドウを右クリックし、3D オブジェクト > スフィアを選択します。
1. 作成したスフィアの名前を RollerAgent にします。
1. RollerAgent を選択し、インスペクタウィンドウを表示します。
1. トランスフォームの位置を`(0, 0.5, 0)`、回転を`(0, 0, 0)`、拡大/縮小を`(1, 1, 1)`に設定します。
1. コンポーネントを追加をクリックします。
1. 物理 > リジッドボディを追加します。

<p align="left">
  <img src="../../images/roller-ball-agent.png"
       alt="The Agent GameObject in the Inspector window"
       width="400" border="10" />
</p>

上記のスクリーンショットには次のセクションで作成する Roller Agent スクリプトが表示されていることに注意してください。
次に、作業を楽にするため、床、ターゲットキューブ、エージェントスフィアを空の GameObject の下にグループ化します。

<p align="left">
  <img src="../../images/roller-ball-hierarchy.png"
       alt="The Hierarchy window"
       width="250" border="10" />
</p>

1. ヒエラルキーウィンドウを右クリックし、空のオブジェクトを作成をクリックします。そして名前を TrainingArea に変更します。
1. TrainingArea の位置が`(0,0,0)`、回転が`(0,0,0)`、拡大/縮小が`(1,1,1)`になるようにリセットします。
1. ヒエラルキーの Floor、Target、RollerAgent を TrainingArea にドラッグします。

## エージェントを実装する

1. RollerAgent ゲームオブジェクトを選択してインスペクターウィンドウを表示します。
1. コンポーネントを追加をクリックします。
1. 一番下の新しいスクリプトをクリックします。
1. スクリプトの名前を RollerAgent にします。
1. 作成して追加をクリックします。

次に、RollerAgent スクリプトを編集します。

1. プロジェクトウィンドウで RollerAgent スクリプトをダブルクリックして開きます。
1. エディタで`using Unity.MLAgents;`と`using Unity.MLAgents.Sensors;`を追加し、親クラスを`MonoBehaviour`から`Agent`に変更します。
1. `Update()`メソッドを削除します。`Start()`メソッドは使用するので残します。

ここまでは ML-Agents を Unity に追加する基本的な手順です。ここからは強化学習を使ってエージェントがキューブに向かうように学習するためのロジックを追加します。
具体的には、Agent 基本クラスの 3 メソッドを実装する必要があります。

- `OnEpisodeBegin()`
- `CollectObservations(VectorSensor sensor)`
- `OnActionReceived(float[] vectorAction)`

これらのメソッドについて説明していきます。
