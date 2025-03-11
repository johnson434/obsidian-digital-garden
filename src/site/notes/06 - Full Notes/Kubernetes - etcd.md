---
{"dg-publish":true,"permalink":"/06-full-notes/kubernetes-etcd/","noteIcon":""}
---

# Tags
- [[03 - Tags/Kubernetes\|Kubernetes]]
## **Kubernetes etcd란?**
`etcd`는 Kubernetes의 분산형 키-값 저장소로 Kubernetes의 컨트롤 노드가 가지고 있는다.
Nodes, PODs, Configs, Secrets, Accounts, Roles, Bindings etc 등이 etcd에 저장된다.

---
## **etcd의 주요 기능**
✅ **클러스터 상태 저장**
- `etcd`는 **Kubernetes의 모든 상태(state)를 저장**하는 역할을 하며, 노드, 파드, 서비스, 설정 정보, 역할 기반 접근 제어(RBAC) 등 모든 데이터를 유지한다.
✅ **고가용성을 위한 분산 시스템**
- `etcd`는 **RAFT 알고리즘**을 기반으로 하는 **분산 합의(consensus) 프로토콜**을 사용하여 **고가용성을 보장**한다.
- 다중 `etcd` 노드가 동작하며, 리더가 장애가 발생하면 **자동으로 새로운 리더를 선출**하여 클러스터를 지속적으로 운영할 수 있도록 한다.
✅ **트랜잭션 지원**
- `etcd`는 **ACID 트랜잭션을 지원**하여 **일관된 데이터 저장 및 조회가 가능**하다.
- Kubernetes는 이를 활용하여 **일관된 클러스터 상태 관리 및 리소스 조정**을 수행한다.
✅ **Watch 메커니즘**
- `etcd`는 **이벤트 기반의 Watch 기능**을 제공하여 **클라이언트가 특정 키의 변경 사항을 실시간으로 감지할 수 있도록 지원**한다.
- Kubernetes 컨트롤러(예: `kube-controller-manager`, `kube-scheduler`)는 이를 활용하여 **클러스터의 상태 변화를 감지하고 즉시 반응**한다.

✅ **Snapshot 및 백업 기능**

- `etcd`는 **스냅샷(snapshot) 기능을 제공**하여 현재 상태를 저장하고 복구할 수 있다.
- `etcdctl snapshot save <file>` 명령어를 이용하면 스냅샷을 생성할 수 있다.

---

## **Kubernetes에서 etcd의 역할**

Kubernetes는 클러스터의 상태를 지속적으로 관리하기 위해 `etcd`를 활용한다. 주요 역할은 다음과 같다.

### **1. 클러스터 데이터 저장소**

- Kubernetes의 **모든 상태 정보**(API 객체, 네트워크 정보, RBAC 설정 등)를 저장한다.
- `kubectl get` 명령어로 조회할 수 있는 모든 Kubernetes 리소스는 결국 `etcd`에 저장된 데이터를 기반으로 한다.

### **2. Control Plane과의 연동**

- `kube-apiserver`는 `etcd`와 직접 통신하여 **요청된 리소스를 생성, 조회, 수정, 삭제**한다.
- `kube-controller-manager`, `kube-scheduler` 등 다른 Control Plane 구성 요소들은 **API Server를 통해 etcd의 상태 변화를 감지하고 반응**한다.

### **3. Leader Election**

- `etcd`는 **RAFT 알고리즘을 사용하여 리더를 선출**하며, Kubernetes의 여러 컨트롤러들은 이 리더 선출 기능을 활용한다.
- 예를 들어, **Controller Manager의 리더 선출**이 이루어질 때 `etcd`의 분산 합의 기능을 사용한다.

### **4. Disaster Recovery 및 High Availability**

- `etcd`는 **Multi-node 분산 저장소**이므로 하나의 노드가 장애가 발생해도 데이터가 유실되지 않는다.
- Kubernetes 클러스터의 중요한 데이터가 손실될 경우, **스냅샷 복원을 통해 etcd 데이터를 복구**할 수 있다.

---

## **etcd 아키텍처 및 구성**

`etcd`는 분산형 Key-Value 저장소이며, **클러스터 환경에서 동작하도록 설계**되었다. 이를 위한 핵심적인 요소들은 다음과 같다.

### **1. 클러스터 토폴로지**

- `etcd`는 **리더-팔로워(Leader-Follower) 아키텍처**를 기반으로 한다.
- 모든 쓰기 요청은 **리더(Leader) 노드에서 수행**되며, 변경 사항이 **팔로워(Follower) 노드로 복제**된다.
- `etcd`는 홀수 개의 노드를 사용하여 **쿼럼(majority voting) 기반으로 데이터 일관성을 유지**한다.
    - 예: 3개 노드일 경우, 최소 2개의 노드가 정상적으로 동작해야 함.
    - 예: 5개 노드일 경우, 최소 3개의 노드가 정상적으로 동작해야 함.

### **2. RAFT Consensus Algorithm**

- `etcd`는 **RAFT 합의 알고리즘**을 기반으로 하여 장애 발생 시에도 안정적인 데이터 저장을 보장한다.
- 리더 선출 및 노드 간 데이터 복제를 통해 **강력한 일관성을 제공**한다.

### **3. 데이터 저장 및 복제**

- `etcd`는 **WAL(Write-Ahead Logging) 방식**을 사용하여 데이터를 디스크에 저장한다.
- 모든 변경 사항은 리더에서 적용된 후, 다른 노드들에 복제된다.
- **데이터는 메모리와 디스크에 저장**되며, 성능을 위해 B+ 트리를 활용한다.

---

## **etcd 운영 및 관리**

운영 환경에서 `etcd`를 안정적으로 관리하기 위해 몇 가지 중요한 사항을 고려해야 한다.

### **1. etcd 백업 및 복구**

- `etcd` 데이터를 보호하려면 **정기적인 백업(snapshot)을 수행해야 함**.
- 백업 생성:
    
    ```bash
    ETCDCTL_API=3 etcdctl snapshot save backup.db
    ```
    
- 백업 복원:
    
    ```bash
    ETCDCTL_API=3 etcdctl snapshot restore backup.db --data-dir=/var/lib/etcd
    ```
    

### **2. etcd 성능 튜닝**
- **SSD 사용**: `etcd`는 높은 쓰기 성능이 필요하므로 SSD를 사용하는 것이 권장됨.
- **압축(compaction) 설정**: 오래된 데이터를 정리하여 디스크 사용량을 줄일 수 있음.
    ```bash
    ETCDCTL_API=3 etcdctl defrag
    ```
- **적절한 etcd 클러스터 크기 유지**: 3개 또는 5개 노드를 유지하는 것이 이상적이며, 과도한 노드 추가는 성능 저하를 초래할 수 있음.

### **3. etcd 보안 설정**
- **TLS 인증 사용**: `etcd` 클러스터 간 통신 및 API 요청 시 **TLS 암호화**를 활성화해야 함.
    ```bash
    etcd --cert-file=/etc/etcd/cert.pem --key-file=/etc/etcd/key.pem
    ```
    
- **RBAC 활성화**: 역할 기반 접근 제어를 적용하여 특정 사용자만 `etcd` 데이터를 수정할 수 있도록 설정 가능.
- **방화벽 설정**: `etcd`는 기본적으로 2379, 2380 포트를 사용하므로 외부 접근을 차단하는 것이 보안에 중요.

---

## **etcd 관련 주요 명령어**

| 명령어                                  | 설명                   |
| ------------------------------------ | -------------------- |
| `etcdctl member list`                | etcd 클러스터의 노드 리스트 조회 |
| `etcdctl snapshot save backup.db`    | etcd 백업 생성           |
| `etcdctl snapshot restore backup.db` | etcd 백업 복구           |
| `etcdctl get /key`                   | 특정 키 조회              |
| `etcdctl put /key value`             | 키-값 저장               |
| `etcdctl del /key`                   | 특정 키 삭제              |
| `etcdctl watch /key`                 | 특정 키의 변경 감지          |
| `etcdctl compaction`                 | 불필요한 데이터 정리          |
| `etcdctl defrag`                     | 디스크 공간 최적화           |

---

## **etcd의 한계 및 고려 사항**

- **스토리지 및 디스크 성능 이슈**: `etcd`는 **로그 기반 스토리지(WAL)를 사용**하기 때문에, 저성능 디스크에서는 성능 저하가 발생할 수 있음.
- **클러스터 확장성 제한**: 너무 많은 노드를 추가하면 RAFT 합의 과정이 복잡해지므로 **최대 5~7개 노드 정도를 유지하는 것이 좋음**.
- **네트워크 안정성 중요**: `etcd`는 네트워크에 의존하는 서비스이므로 **고가용성 환경에서 네트워크 안정성이 보장되어야 함**.