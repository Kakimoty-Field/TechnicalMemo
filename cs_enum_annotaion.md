# enum の 属性取得 には気をつける

ハマったのでメモ。

# 構成
![info](https://dl.dropboxusercontent.com/s/v4usi627xm09ox3/20190208-00_01.png)

- [DLL] に 列挙型を定義する
- 列挙型の各メンバーには `属性` が指定されている
- [exe など] は、DLL の 列挙型から `属性` を取得して利用する

# ハマったこと
デプロイ後、[DLL] 内の列挙型にメンバーを追加してビルドしなおす。
[exe など] はビルドせず、[DLL] を差し替えて実行すると、意図した結果が得られないパターンがある。

# 当システムの初期状態
## ソース
```cs:enumA.dll.cs
namespace KakimoDLL
{
  /// <summary>
  /// カスタム属性
  /// </summary>
  public class TempAttribute : Attribute
  {
    /// <summary>
    /// コンストラクタ
    /// </summary>
    /// <param name="val"></param>
    public TempAttribute(string val)
    {
      Value = val;
    }
    /// <summary>
    /// メンバ
    /// </summary>
    public string Value { get; set; }
  }
  /// <summary>
  /// 検証用列挙型
  /// </summary>
  public enum EnumA
  {
    [Temp("001")]
    Member1,
    [Temp("002")]
    Member2,
    [Temp("003")]
    Member3
  }
}
```

```cs:exeとか.cs
using KakimoDLL;

namespace KakimoExe
{
  class Program
  {
    static void Main(string[] args)
    {
      var enumVal = EnumA.Member2;  // Member2 メンバの属性を取得してみる
      
      var enumType = enumVal.GetType();
      var enumField = enumType.GetField(Enum.GetName(typeof(EnumA), enumVal));
      
      var attr =(TempAttribute[])enumField.GetCustomAttributes(typeof(TempAttribute), false);
      Console.WriteLine(attr[0].Value);   //
    }
  }
}
```
## 実行結果
```
002
```

# DLL 改修後も、一応動くパターン
## ソース (列挙型部分のみ)
```
  /// <summary>
  /// 検証用列挙型
  /// </summary>
  public enum EnumA
  {
    [Temp("001")]
    Member1,
    [Temp("002")]
    Member2,
    [Temp("003")]
    Member3,
    [Temp("004")]
    Member4,
  }
```
## 実行結果
```
002
```

# DLL 改修後、意図しない結果になるパターン
## ソース (列挙型部分のみ)
```
  /// <summary>
  /// 検証用列挙型
  /// </summary>
  public enum EnumA
  {
    [Temp("000")]
    Member0,
    [Temp("001")]
    Member1,
    [Temp("002")]
    Member2,
    [Temp("003")]
    Member3,
  }
```
## 実行結果
```
001
(期待する結果は '002' )
```

# 原因

>列挙型はプログラムの内部では整数として扱われていて、 整数型に変換することでその値を取り出すことが出来ます。 特に値や型を指定しなければ、列挙型は int として扱われ、 各メンバーは宣言した順番に 0, 1, 2, …, n となります。

[列挙型 - ++C++; // 未確認飛行 C  ](https://ufcpp.net/study/csharp/st_enum.html)

[exe など] がコンパイルされた時点で `EnumA.Member2 == 1` です。
[exe など] が実行されると、`EnumA の 値が 1 のメンバーに追加されている属性` を取得しようとします。 

"DLL 改修に意図しない結果になるパターン" のように、`EnumA.Member2` の宣言順をかえてしまうようなメンバー追加をしてしまうと、[exe など] が意図していた結果が取得できなくなります。

# 対応策

1. enum メンバーの順番が変わらないように追加宣言する
2. enum メンバーに明示的に値を指定しておく
3. そもそもの実装を考えなおす

