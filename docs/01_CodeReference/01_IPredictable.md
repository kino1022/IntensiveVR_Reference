### 分類: Interface

### ジェネクリス制限: なし

## 概要

このインターフェースはfloat型の指定した秒数後におけるT型の変数の内容を予測できるクラスに対して実装されます。

## 実装

```csharp
namespace Movement.Interface {  
    /// <summary>  
    /// 指定された秒数後の状態を予測できるクラスに対して約束するインターフェース  
    /// </summary>  
    /// <typeparam name="T"></typeparam>    public interface IPredictable<T> {  
        /// <summary>  
        /// 数秒後の状態を取得する  
        /// </summary>  
        /// <param name="currentTime">現在の時間</param>  
        /// <param name="predictionTime">何秒後の状態を取得するか</param>  
        /// <returns></returns>        T Predict(float currentTime, float predictionTime);  
            }  
}
```

## 説明

このインターフェースは弾道予測線などで利用される物体の移動方向と移動速度を予測するために定義されました。

実装に際しては現在の時間と、予測したい時間を渡してPredictが呼ばれたならば、Tに当たる型の変数の状態を予測して返さなければなりません。

また、Predictは頻繁に呼ばれることが予想されるため、内部でのGameObject型に対するNullチェックはよほどのことがない限りしないほうが良いです。（[[docs/02_TechnicalInfo/01_GameObject型やMonoBehaviourに対してのNull比較|01_GameObject型やMonoBehaviourに対してのNull比較]]を参照）