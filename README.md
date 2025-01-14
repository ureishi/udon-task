# UdonTask
![](https://img.shields.io/badge/unity-2022.3+-000.svg)
[![Releases](https://img.shields.io/github/release/chiugame/udon-task.svg)](https://github.com/chiugame/udon-task/releases)

This package enables the execution of Udon from a separate thread.

Udonを別スレッドで実行可能にするやつ。

## Installation
[Iwashi Packages](https://vpm.iwa.si/)を開いて「Add to VCC」を押してVCCからUdonTaskを追加します。

## Usage
### コールバックを使う
```csharp
using UnityEngine;
using UdonSharp;
using Iwashi.UdonTask;

public class UdonTaskSample : UdonSharpBehaviour
{
  private void Start()
  {
    UdonTask.New(this, nameof(ExecuteTask), nameof(OnFailed), nameof(OnSuccess));
  }

  public void ExecuteTask()
  {
    /* ここに重たい処理を書く。
     * メインスレッドでしか触れないものは当然触れません。要注意！
     * 触れないものを触った場合UdonによってUnityEngine.Object.ToStringが呼ばれます */
  }

  public void OnFailed()
  {
    Debug.LogError("失敗！");
  }

  public void OnSuccess()
  {
    Debug.Log("成功！");
  }
}
```

### Updateで待つ
```csharp
using UnityEngine;
using UdonSharp;
using Iwashi.UdonTask;

public class UdonTaskSample : UdonSharpBehaviour
{
  private UdonTask _task;

  private void Start()
  {
    _task = UdonTask.New(this, nameof(ExecuteTask));
  }

  private void Update()
  {
    if (_task.IsComplete())
    {
      // 完了時の処理
    }
  }

  public void ExecuteTask()
  {
    // ここに重たい処理を書く。
  }
}
```

- 第1引数にUdonBehaviourかUdonSharpBehaviourを設定できます。thisを使うと自身のUdonSharpBehaviourを指定できます。
- 第5引数にタイムアウト時間を設定できます。省略すると30秒でタイムアウトします。
- 戻り値のUdonTaskのResultStatus()で進行状況のEnumを得られます。（NotExecuted、Running、Success、Failed）

## Samples
UnityのPackageManagerのUdonTask→Samplesからサンプルシーンをインポートできます。

サンプルシーンでは[Base64エンコード](https://gist.githubusercontent.com/chiugame/76a08e9e2cb0735b1c7ff848e335b30f/raw/b956b266e4f0c35b8fde9edb284fe7efc300ba05/SamplePictures.txt)された17枚の画像データを高速で読み込むテストができます。

[テストワールド](https://vrchat.com/home/world/wrld_687f009c-fffb-4532-bb55-c075788a33b1)
