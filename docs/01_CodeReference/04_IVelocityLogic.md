### 分類: Interface

## 概要

物体の持つ運動量そのものを管理するクラスに対して約束するインターフェース

## 実装
```csharp
/// <summary>  
/// 運動の力を管理するクラスに対して約束するインターフェース  
/// </summary>  
public interface IVelocityManager {  
    /// <summary>  
    /// 現在の運動の力  
    /// </summary>  
    Vector3 Velocity { get; }  
    /// <summary>  
    /// 運動量が有効であるかどうか  
    /// </summary>  
    bool EnableVelocity { get; set; }  
}
```

## 説明

動的な運動量そのものを管理するために定義されたインターフェースです。

これを実装したクラスが管理している運動量を取得する場合にはVelocityを参照してください。

利用先に予定されているAMovementExecutorではEnableVelocityによってVelocityの値を参照するかどうかコントロールされているように、EnableVelocityはその真偽によって運動量を考慮するかしないかを表現するよう設計されています。

