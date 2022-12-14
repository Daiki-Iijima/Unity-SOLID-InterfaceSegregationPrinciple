# インターフェイス分離の原則

## このリポジトリについて

このリポジトリは、SOLID原則の1つ`インターフェイス分離の原則(InterfaceSegregationPrinciple)`について、Unityで実践するためにはどのように実装するのかを例にあげて解説しています。

## インターフェイス分離の原則の概略

基本的には、どんなクライアントも使用しないメソッド(プロパティ)に依存することを強制されるべきではないということです。

端的にいうと、この原則は`インターフェイスが強制する、実装についての原則`です。

## 例 : ゲーム内のオブジェクト用のInterfaceを作る

エンティティ(ゲーム内のオブジェクト全体)インターフェイスは、その名の通り、ゲーム内に存在するすべてのオブジェクトに対して実装されるべきだと考えられて作られたインターフェイスになっています。

```c#
//  ゲームに存在するすべてのオブジェクトが実装するインターフェイス
public interface IEntity
{
    //  ステータス
    int STR { get; }
    int STA { get; }
    int CON { get; }
    int WIS { get; }
    int INT { get; }
    int CHA { get; }

    //  魅了効果をかける
    void Mesmerize();

    //  HP
    float Health { get; set; }
    int HealthMax { get; }
    int ModifyHealth(int amount);

    //  シールド
    float Shield { get; set; }
    int ShieldMax { get; }
    int ModifyShield(int amount);
}
```

しかし、このインターフェイスを実際にすべてのオブジェクトに実装しようとすると、プレイヤーやNPCは、ステータスを持っていてもいいかもしれません。

ゲームに存在するすべてのオブジェクトにこのインターフェイスを実装したとすると、例えばドアや建物がHPを持っているのは何となく想像ができます。しかし、INT(知力)を持っているのは、どうもおかしい気がします。

この現象の原因は、`インターフェイスが多くの実装を強制している`点にあります。

インターフェイスが強制するのは、そのインターフェイスを実装したクラスすべてが実装していても違和感のない、必然的な実装であるべきです。

## 原因

このようなインターフェイスができる原因として、`すべてのゲーム内のオブジェクト抽象化して同じフローで処理したい`、`記述するコードを少なくしたい`という願望からきているものと推測します。

しかし、実際にはインターフェイスはできる限り分離しておくことで、結果的に無駄な処理(ドアにINTがあるような処理)を記述しなくて済むようになります。

## 改善するにはどうするか

この無駄な実装を強制してしまうインターフェイスへの対策として、`インターフェイスを分離してオブジェクトが必要な実装だけをするようなインターフェイス`を定義していけばよいということになります。

IEntityインターフェースは必要なく、IEntityインターフェイスからそれぞれ項目ごとに分離した`複数の小さいインターフェイスを定義`していけばいいということになります。

## 修正したコード

IEntityインターフェイスが強制していた実装を要素ごとに分割して固めた

- IHeath : HPを持っている

  ```c#
  /// <summary>
  /// HPを持たせるInterface
  /// HPだけを持っていて状態を持っていないオブジェクトもいるので分離している
  /// </summary>
  public interface IHealth
  {
      int HealthMax { get; }
      float Health { get; set; }
      int ModifyHealth(int amount);
  }
  ```

- IShield : シールドを持っている

  ```c#
  /// <summary>
  /// シールドを持たせるインターフェイス
  /// </summary>
  public interface IShield
  {
      int ShieldMax { get; }
      float Shield { get; set; }
      int ModifyShield(int amount);
  }
  ```

- IStats : ステータスを持っている(HP,状態)

  ```c#
  /// <summary>
  /// ステータスを持たせるインターフェイス
  /// </summary>
  public interface IStats : IHealth
  {
      //  ステータス
      int STR { get; }
      int STA { get; }
      int CON { get; }
      int WIS { get; }
      int INT { get; }
      int CHA { get; }
  }
  ```


- IMesmerized : 魅了可能

  ```c#
  /// <summary>
  /// 魅了可能インターフェイス
  /// </summary>
  public interface IMesmerized
  {
      void Mesmerize();
  }
  ```
