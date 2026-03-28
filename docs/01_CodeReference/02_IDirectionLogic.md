### 分類: Interface

## 概要

物体の持つ運動量のうち運動の方向、すなわち標準化された運動ベクトルを管理する事を約束するインターフェース

## 実装
```csharp
/// <summary>  
/// 方向を示すロジックのクラスに対して約束するインターフェース  
/// </summary>  
public interface IDirectionLogic {  
    Vector3 Direction { get; }  
}
```
## 説明

動的な運動量を、動的な運動方向と動的な運動量に分解して管理するために定義されたインターフェースです。

実用においては以下のような実装がなされると考えられます。
```csharp
[Serializable]  
public class GoingEntityDirectionLogic : IPredictableDirectionLogic { 
 
    [SerializeField]  
    private Entity.Entity entity;  
  
    [SerializeField]  
    private GameObject owner;  
  
    public Entity.Entity Entity {  
        get => entity;  
        set => entity = value;  
    }    
    
    public GameObject Owner {  
        get => owner;  
        set => owner = value;  
    }  
    
    public Vector3 Direction => CalculateDirection().normalized;  
  
    public GoingEntityDirectionLogic() { }  
  
    public Vector3 CalculateDirection() {  
        if (owner is null) {  
            Debug.LogWarning($"{GetType().Name} : Ownerがnullです");  
            return Vector3.zero;  
        }  
        if (entity is null) {  
            Debug.LogWarning($"{GetType().Name} : Entityがnullです");  
            return Vector3.zero;  
        }        return (entity.transform.position -owner.transform.position).normalized;  
    }  
    public Vector3 Predict(float currentTime, float predictionTime) {  
        //別にEntityのVelocityをとってそこから予測したっていい  
        return CalculateDirection();  
    }
}
```
これは代入されたEntityの座標の方向を示し続けるIDirectionLogicの実装例です。
(実際に制作されたものからの流用なので[01_IPredictable](docs/01_CodeReference/01_IPredictable.md)も継承しています)
