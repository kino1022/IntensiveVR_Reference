### 分類: Interface

## 概要

物体の持つ運動量のうち運動の速さ、すなわちfloat型で表される速度を管理する事を約束するインターフェース

## 実装
```csharp
/// <summary>  
/// 運動の力を持つクラスに対して約束するインターフェース  
/// </summary>  
public interface IForceLogic {  
    /// <summary>  
    /// 現在の運動の力  
    /// </summary>  
    float Force { get; }  
}
```

## 説明

動的な運動量を、動的な運動方向と動的な運動量に分解して管理するために定義されたインターフェースです。
