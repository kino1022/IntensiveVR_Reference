
**分類: 抽象コンポーネント**

**ジェネリクス制限: [04_IVelocityManager](04_IVelocityManager.md)**

**継承: SerializedMonoBehaviour**

**実装: [05_IMovementExecutor](05_IMovementExecutor.md)**

## ゲーデザ向け概要
このコンポーネントを継承した派生コンポーネントをオブジェクトに対してアタッチする事で、同じオブジェクトに対してアタッチされているRigidBodyの運動量を、毎フレーム算出された値に上書きします。

## 概要
RIgidBodyに対して運動量を適用するIVelocityManagerの抽象実装クラスです。
運動の制御にRigidBodyを利用するGameObjectに対してはこのクラスの派生クラスを利用してください。
CharacterControllerを利用する場合に使用するコンポーネントの実装はプロジェクトの内容を理由とし現在見送られています。
必要になった場合は藤田まで申しつけてください。

## 実装
```cshrap
public abstract class AMovementExecutor<T> : SerializedMonoBehaviour,IConstructable, IMovementExecutor<T> where T : IVelocityManager {  

    [SerializeField]  
    [Tooltip("現在の運動量")]  
    [ReadOnly]  
    private Vector3 totalVelocity = Vector3.zero;  
  
    [SerializeField]  
    [Tooltip("運動の適用を有効にするかどうか")]  
    private bool enableExecution = true;  
  
    [OdinSerialize]  
    [Tooltip("運動の力を管理するクラス")]  
    private List<T> velocities = new();  
    
    [SerializeField]  
    [Tooltip("運動に利用するRigidBody")]  
    private Rigidbody rigidBody;  
    
    public IReadOnlyList<T> Velocities => velocities;  
    
    public bool EnableExecution {  
        get => enableExecution;  
        set => enableExecution = value;  
    }  
    private IObjectResolver _resolver;  
  
    [Inject]  
    public void Construct(IObjectResolver resolver) {  
        _resolver = resolver;  
    }  
    private void Start() {  
        rigidBody ??= GetRigidBody();  
        OnStart();  
    }  
    private void FixedUpdate() {  
        OnPreFixedUpdate();       
        totalVelocity = CalculateVelocity();  
        OnPreApplyVelocity(ref totalVelocity);  
        ApplyVelocity(totalVelocity);  
        OnPostFixedUpdate();  
    }  
    public bool AddVelocityManager(T manager) {  
        if (manager is null) {  
            return false;  
        }  
        if (Velocities.Contains(manager)) {  
            return false;  
        }        velocities.Add(manager);  
        return true;  
    }  
    public bool RemoveVelocityManager(T manager) {  
        if (manager is null) {  
            return false;  
        }        
        return velocities.Remove(manager);  
    }  
    private Vector3 CalculateVelocity() {  
        Vector3 vec = Vector3.zero;  
        if (velocities.Count == 0) {  
            return vec;  
        }        
        foreach (var velocity in velocities) {  
            if (velocity is null) {  
                continue;  
            }           
            if (!velocity.EnableVelocity) {  
                continue;  
            }            
            vec += velocity.Velocity;  
        }        
        return vec;  
    }  
    protected virtual void ApplyVelocity(Vector3 vec) {  
        rigidBody ??= GetRigidBody();  
        rigidBody.linearVelocity = vec;  
    }    
    
    private Rigidbody GetRigidBody() => _resolver is not null ? _resolver.Resolve<Rigidbody>() : rigidBody; 
     
    protected virtual void OnStart() {}  
    protected virtual void OnPreFixedUpdate () {}  
    protected virtual void OnPostFixedUpdate () {}  
    protected virtual void OnPreApplyVelocity (ref Vector3 vec) {}  
}
```

## 説明

このクラスはIVelocityManagerの管理とRigidBodyに対しての運動量の適用を適切に行うように設計されています。

またApplyVelocityメソッドは仮想化されているのでRIgidBodyに対しての適用ロジックは継承先で変更することが可能です。
極端な話をするならばOnStartメソッドとApplyVelocityのオーバーライドでRigidbodyではなくCharacterControllerを対象にした運動量制御も可能です。