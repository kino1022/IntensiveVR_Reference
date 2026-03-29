
**分類: Interface**

**ジェネリクス制限: IVelocityManager**

## 概要

IVelocityManagerから得られた運動量をCharacterControllerやRigidBodyに対して適用する事を約束するインターフェース

## 実装
```csharp
public interface IMovementExecutor<T> where T : IVelocityManager{  
    IReadOnlyList<T> Velocities { get; }  
    bool EnableExecution { get; set; }  
    bool AddVelocityManager (T manager);  
    bool RemoveVelocityManager (T manager);  
}
```

## 説明

IVelocityManagerをリスト形式で管理して、管理下のIVelocityManager.Velocityを合算して得られた総運動量をCharacterControllerやRigidBodyに対して適用する事を目的として定義されたインターフェースです。

メソッドやプロパティは以下のものを制御する目的で実装されます。
### IReadOnlyList Velocities
管理下にあるIVelocityManagerを外部に対して提供します。

### bool EnbaleExecution
運動量の適用を行うかどうかを外部からコントロールするために公開される真偽値のプロパティです。
falseの場合は運動量の適用を行わないように実装してください。

### bool AddVelocityManager (T manager)
managerとして渡したIVelocityManagerを運動量の算出に使用するようにします。

### bool RemoveVelocityManager (T manager)
managerとして渡されたIVelocityManageが管轄下にあるのであれば運動量の算出から除外します。
