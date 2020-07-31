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

### エージェントの初期化とリセット

ML-Agents における訓練プロセスは、エージェント(球)がタスクを解決しようとするエピソードの実行を含みます。各エピソードはエージェント(球)がタスクを解決する(キューブに到達する)、失敗する(プラットフォームから落下する)、制限時間をオーバーするまで続きます。
各エピソードの開始時に、`OnEpisodeBegin()`メソッドが呼ばれ、次のエピソード用の環境の準備をします。通常、エージェントがさまざまな条件下でタスクを解決できるようシーンはランダムに初期化されます。

この例では、エージェント(球)がターゲット(キューブ)に到達するとエピソードが終了し、メソッドでターゲットはランダムな場所に移動します。さらに、エージェントがプラットフォームから落下すると`OnEpisodeBegin()`メソッドでエージェントを床の上に戻します。

ターゲット(キューブ)を移動させるために、ターゲットの Transform への参照が必要です。Transform はゲームオブジェクトの位置、向き、拡大縮小率を格納したものです。
Transform への参照を取得するために、`Transform`タイプのパブリックフィールドを RollerAgent クラスに用意します。Unity ではコンポーネントのパブリックフィールドはインスペクターウィンドウに表示され、Unity エディタでターゲットとして使用する GameObject を選択できます。

エージェントの速度をリセットする(また後でエージェントを動かすために力を加える)ために Rigidbody コンポーネントへの参照が必要です。Unity の[Rigidbody](https://docs.unity3d.com/ScriptReference/Rigidbody.html)は物理シミュレーションのための主要な要素です。(詳しくは[Unity の物理](https://docs.unity3d.com/Manual/PhysicsSection.html)を参照してください。)Rigidbody コンポーネントは Agent スクリプトと同じゲームオブジェクト上にあるので、`GameObject.GetComponent<T>()`メソッドを使用して参照を取得できます。

ここまでで、RollerAgent スクリプトは次のようになります。

```csharp
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    void Start () {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            // If the Agent fell, zero its momentum
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3( 0, 0.5f, 0);
        }

        // Move the target to a new spot
        Target.localPosition = new Vector3(Random.value * 8 - 4,
                                           0.5f,
                                           Random.value * 8 - 4);
    }
}
```

続いて、`Agent.CollectObservations(VectorSensor sensor)`メソッドを実装しましょう。

### 環境の観測

エージェントは情報を脳に送信し、脳は情報を意思決定のために使います。エージェントを訓練する(または学習済みのモデルを使う)と、データはニューラルネットワークに特徴ベクトルとして送られます。エージェントが学習タスクを成功させるには、正しい情報を供給する必要があります。どのような情報を収集するかの目安は、問題を解決するときに必要な情報を検討することです。

今回の場合、ターゲットの位置、エージェントの位置、エージェントの速度が必要です。これらの情報によって、エージェントは自身のスピードをコントロールすることを学習しターゲットを行き過ぎないよう学習します。そしてプラットフォームから落ちないことも学習します。全体として、エージェントは以下の実装のように 8 つの値を観測します。

```csharp
public override void CollectObservations(VectorSensor sensor)
{
    // Target and Agent positions
    sensor.AddObservation(Target.localPosition);
    sensor.AddObservation(this.transform.localPosition);

    // Agent velocity
    sensor.AddObservation(rBody.velocity.x);
    sensor.AddObservation(rBody.velocity.z);
}
```

### 行動と報酬の割り当て

Agent のコードの最後の部分は、行動を受け取り、報酬を割り当てる`Agent.OnActionReceived()` メソッドです。

#### 行動

ターゲットに向かって移動する今回のタスクを解決するために、エージェント(球)は X 方向と Z 方向に移動できる必要があります。そのため、エージェントには 2 つのアクションを設定します。1 つ目のアクションは X 方向に加えられる力を決定し、2 つ目のアクションは Z 方向に加えられる力を決定します。(もしエージェントが 3 次元の動きを許されているなら、3 つ目のアクションが必要でしょう)

RollerAgent は`action[]`配列の値を、Rigidbody コンポーネントの`Rigidbody.AddForce`関数を使用して適用します。

```csharp
Vector3 controlSignal = Vector3.zero;
controlSignal.x = action[0];
controlSignal.z = action[1];
rBody.AddForce(controlSignal * forceMultiplier);
```

#### 報酬

強化学習には報酬が必要です。報酬は`OnActionReceived()`関数で割り当てます。学習アルゴリズムはエージェントに割り当てられた報酬を使用して、学習中エージェントに最適なアクションを提供しているか判断します。割り当てられたタスクを完了したエージェントに報酬を与えるため、ターゲットに到達したエージェントには 1.0 の報酬を与えます。

RollerAgent はターゲットへの到達を検知します。到達したら`Agent.SetReward()`メソッドを呼び出し 1.0 の報酬を割り当てます。そして`EndEpisode()`メソッドを使用して 1 回のエピソードを終了します。

```csharp
float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);
// Reached target
if (distanceToTarget < 1.42f)
{
    SetReward(1.0f);
    EndEpisode();
}
```

最後に、エージェントがプラットフォームから落下した場合、リセットのためにエピソードを終了します。

```csharp
// Fell off platform
if (this.transform.localPosition.y < 0)
{
    EndEpisode();
}
```

#### OnActionReceived()

上記のアクションと報酬のロジックにより、最終的な`OnActionReceived()`関数は次のようになります。

```csharp
public float forceMultiplier = 10;
public override void OnActionReceived(float[] vectorAction)
{
    // Actions, size = 2
    Vector3 controlSignal = Vector3.zero;
    controlSignal.x = vectorAction[0];
    controlSignal.z = vectorAction[1];
    rBody.AddForce(controlSignal * forceMultiplier);

    // Rewards
    float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

    // Reached target
    if (distanceToTarget < 1.42f)
    {
        SetReward(1.0f);
        EndEpisode();
    }

    // Fell off platform
    if (this.transform.localPosition.y < 0)
    {
        EndEpisode();
    }
}
```

翻訳中・・・

## 環境のテスト

翻訳中・・・
