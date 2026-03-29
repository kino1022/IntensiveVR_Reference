
**分類: コンポーネント**

**継承: SerialziedMonoBehaviour**

## ゲーデザ向け概要

このコンポーネントを当たり判定のあるオブジェクトに対してアタッチする事でCollisionCallbackExecutorに登録した処理を衝突時に呼び出す事ができます

衝突時の処理を分岐させるためにアタッチしたオブジェクトの親子のいずれかにITagContainerHolderを実装したコンポーネントがアタッチされている事が望ましいです。

## 概要

このコンポーネントは同じコンポーネントと当たり判定を持ったオブジェクトと衝突した際に、自身と相手のCallbackableCollisionCollider(以下C3)から得られた情報をもとにして、OnCollisionCallbackEventBusを他のインスタンスに対してパブリッシュします。

## 実装

```csharp
using System;  
using System.Collections.Generic;  
using AttributeTag;  
using Item;  
using MessagePipe;  
using Sirenix.OdinInspector;  
using Sirenix.Serialization;  
using UnityEngine;  
using Utility;  
  
namespace Collision {  
    /// <summary>  
    /// 衝突時にコールバック処理を行うオブジェクトにアタッチするコンポーネント  
    /// </summary>  
    [RequireComponent(typeof(Collider))]  
    public class CallbackableCollisionCollider : SerializedMonoBehaviour {  
  
        [OdinSerialize]  
        [ReadOnly]  
        private ITagContainerHolder _containerHolder;  
                [SerializeField]  
        [Tooltip("このオブジェクトの持っている運動量")]  
        [ReadOnly]  
        private Vector3 velocity;  
                [SerializeField]  
        [TableList]  
        [ReadOnly]  
        private List<Vector3> previousPositions = new(10);  
  
        [SerializeField]  
        [Tooltip("何フレーム分座標を保持するか")]  
        private int previousPositionsLength = 10;  
        public Vector3 Velocity => velocity;  
  
        public IAttributeTagContainer Tags  {  
            get {  
                _containerHolder ??=　gameObject.GetComponentFromWhole<ITagContainerHolder>();  
                return _containerHolder is null ? new AttributeTagContainer() : _containerHolder.Tags;  
            }        }        private IPublisher<OnCollisionCallbackEventBus> _collisionPublisher;  
  
        private void Start() {  
            _collisionPublisher = GlobalMessagePipe.GetPublisher<OnCollisionCallbackEventBus>();  
        }  
        private void Update() {  
            previousPositions.Add(transform.position);  
            if (previousPositions.Count > previousPositionsLength) {  
                previousPositions.RemoveAt(0);  
            }            velocity = previousPositions.CalculateVelocity();  
        }  
        private void OnCollisionEnter(UnityEngine.Collision other) {  
  
            var callbackable = other.gameObject.GetComponentFromWhole<CallbackableCollisionCollider>();  
            if (callbackable == null) return;  
            //自分の衝突コンテキストの生成  
            var contextA = new CollisionObjectContext() {  
                Object = gameObject,  
                Tags = gameObject.GetComponent<IAttributeTagContainer>(),  
                Velocity = velocity,  
            };  
            //相手の衝突コンテキストの生成  
            var contextB = new CollisionObjectContext() {  
                Object = callbackable.gameObject,  
                Tags = callbackable.Tags,  
                Velocity = callbackable.Velocity,  
            };  
            var context = new OnCollisionContext(contextA, contextB, other.GetContact(0).point);  
            _collisionPublisher.Publish(new OnCollisionCallbackEventBus(context));  
        } 
    }  
}
```

## 説明

処理としては[.(#^579ef6)]の見たまんまなのですが、MessagePipeを使って処理の実行を行う事で拡張性と疎結合性の向上が測られています。