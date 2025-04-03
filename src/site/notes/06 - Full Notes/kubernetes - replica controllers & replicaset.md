---
{"dg-publish":true,"permalink":"/06-full-notes/kubernetes-replica-controllers-and-replicaset/","noteIcon":""}
---

# Tags
- [[03 - Tags/Kubernetes\|Kubernetes]]
### **ReplicationController (RC) vs. ReplicaSet (RS) 차이점**

ReplicationController(RC)와 ReplicaSet(RS)는 둘 다 **지정된 개수의 파드(Pod)를 유지하는 역할**을 하지만, **ReplicaSet은 ReplicationController의 발전된 버전**입니다.

---

## 🟢 **1. 주요 차이점**

|                        | **ReplicationController (RC)** | **ReplicaSet (RS)**                              |
| ---------------------- | ------------------------------ | ------------------------------------------------ |
| **지원하는 `selector` 형식** | 단순 `key: value` 매칭             | `matchLabels` + `matchExpressions`(조건 기반 필터링 가능) |
| **기능적 발전**             | Kubernetes 초창기 컨트롤러            | ReplicationController 개선판                        |
| **주요 사용 사례**           | Kubernetes 1.8 이전 버전에서 사용      | 일반적으로 **Deployment**에서 내부적으로 사용                  |
| **권장 여부**              | **Deprecated (사용 비추천)**        | Kubernetes에서 공식적으로 RC 대신 RS 사용 권장                |

---

## 🔍 **2. 가장 큰 차이점: `selector` 기능**

**ReplicationController는 `selector`에 단순 `key-value` 매칭만 가능하지만, ReplicaSet은 더 강력한 `matchExpressions`를 지원**합니다.

### **ReplicationController 예제 (단순 key-value 매칭)**
아래 예제를 보면 matchLabels란 표현 없이 바로 `key`, `value`를 받는다. 이 값은 template의 labels와 매핑된다.
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx  # 단순한 key-value 매칭만 가능
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```

### **ReplicaSet 예제 (`matchExpressions` 지원)**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx  # 기본적인 key-value 매칭
    matchExpressions:
      - key: environment
        operator: In
        values: ["prod", "staging"]  # ✅ "prod" 또는 "staging" 환경의 파드만 선택
  template:
    metadata:
      labels:
        app: nginx
        environment: prod  # ✅ matchExpressions 조건 충족
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```

👉 **`matchExpressions` 사용 덕분에, 특정 조건을 만족하는 파드만 선택할 수 있습니다!**  
(RC에서는 불가능했던 기능)

---

## 🔥 **3. Deployment와 함께 사용하는 것?**

### **ReplicationController (RC)**

- 직접 사용하기보다는, **Deployment를 사용하여 관리하는 것이 권장됨**
- Deployment는 **Rolling Update**, **Canary Release**, **자동 롤백** 등의 기능을 제공

### **ReplicaSet (RS)**

- 일반적으로 직접 사용하지 않고, **Deployment의 내부 구성 요소로 사용됨**
- **Deployment → ReplicaSet → Pod** 구조로 동작

```sh
kubectl get rs  # Deployment가 생성한 ReplicaSet 확인 가능
```

---

## **4. 결론: RC는 폐기 예정, RS 사용 추천**

- **ReplicationController(RC)**는 Kubernetes 1.8 이후 사실상 **사용이 권장되지 않음**
- **ReplicaSet(RS)**은 **Deployment 내부적으로 사용**되며, **별도로 사용할 일은 많지 않음**
- **추천 방식**: **Deployment 사용 → 내부적으로 ReplicaSet이 파드 관리**

### ✅ **💡 추천 방법**

```sh
kubectl create deployment nginx --image=nginx --replicas=3
```

👉 위 명령어를 실행하면 **Deployment → ReplicaSet → Pod** 구조가 자동으로 생성됩니다.

---