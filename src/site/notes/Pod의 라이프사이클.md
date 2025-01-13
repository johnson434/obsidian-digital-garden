---
{"dg-publish":true,"permalink":"/pod/","dgPassFrontmatter":true}
---

[[03 - Tags/Kubernetes Pod\|Kubernetes Pod]]

# 단서 질문
# 핵심 요약

# 핵심 필기
## Pod Lifecycle
Pod는 `Pending` 페이즈로 시작합니다.
Pod 내부에 기본 컨테이너가 하나라도 실행된다면 `Running` 페이즈가 됩니다.
이후에 Pod가 종료되면 Pod 내부에 컨테이너 중에 실패한 게 있다면 `Failed` 없다면 `Succeeded` 페이즈가 됩니다.
Pod가 생성되면 UID를 할당 받으며 특정 노드에 스케쥴링 됩니다. Pod가 제거(Termination)되거나 삭제(Deletion)될 때까지 해당 노드에서 실행됩니다.
만약에, 노드가 죽는다면 해당 노드에서 실행 중이거나 스케쥴링 된 Pod는 모두 `deletion`으로 마킹됩니다.
> [!NOTE]
> **restartPolicy**는 컨테이너를 재실행할뿐. Pod 자체를 재실행하지 않습니다. 따라서, 실행 중인 노드에서 그대로 동작합니다.
## Pod Lifetime
Pod가 실행되는 동안, kubelet은 fault를 핸들링하기 위해서 컨테이너를 재실행할 수 있습니다.
쿠버네티스는 Pod 내부에 컨테이너들의 상태를 추적하고 Pod를 회복하기 위한 조치를 취합니다.

Kubernetes API에서 Pod는 사양(specification)과 실제 상태(actual status)를 가집니다.
Pod의 상태는 Pod conditions 배열로 이뤄집니다.

Pod는 Lifetime동안 단, 한번만 스케쥴링됩니다. 특정 노드에 할당하는 걸 바인딩이라 표현하며 어떤 노드에 할당할지 선택하는 과정을 스케쥴링이라고 합니다.
Pod가 멈추거나, 제거되기 전까지 해당 노드에서 실행됩니다.
만약에, Pod가 선택된 노드에서 시작이 불가능한 경우(Pod에 스케쥴링은 됐지만 시작 되기 전에 노드에 문제가 생기는 경우)엔 Pod는 평생 시작하지 못합니다.

`Pod Scheduling Readiness`를 통해서 `scheduling gates`가 모두 제거될 때까지 스케쥴링을 연기할 수 있습니다.

k8s는 높은 수준의 추상화를 사용합니다. 이를 컨트롤러라고 합니다. 
컨트롤러는 상대적으로 폐지 가능한 Pod 인스턴스들을 관리합니다.

UID를 통해 정의된 Pod는 절대 다른 노드로 리스케쥴링(rescheduled)되지 않습니다.
대신에, Pod는 거의 동일한 새로운 Pod로 교체될 수 있습니다. 만약, Pod가 대체된다면 이전 Pod와 동일한 이름(.metadata.name)을 가집니다. 그러나, UID는 변경됩니다.

k8s는 Pod가 교체될 때, 원본 Pod와 동일한 노드로 스케쥴링 되는 것을 보장하지 않습니다. 
즉, Pod는 다른 노드로 **리스케쥴링은 되지 않지만** Pod가 **다른 Pod로 교체될 수 있으며 이 과정에서 다른 노드에 배치될 수 있습니다.**

## Associated lifetimes
Pod와 동일한 lifetime을 가진다는 말은 Pod가 죽었을 때(Pod의 UID가 변경됨) 해당 오브젝트도 죽는다는 의미입니다.
Pod가 죽었거나 혹은 identical replacement(Pod 교체가 일어나동일한 Pod명을 가지지만, UID가 변경됨)가 일어나면 관련 오브젝트도(ex. 볼륨) destroyed 후에 다시 만들어집니다.

### Pods and fault recovery
Pod 내부에 컨테이너에 문제가 생긴다면 k8s에서 컨테이너를 재시작을 시도할 수도 있습니다.
그러나 클러스터가 복구할 수 없는 방식으로 Pod가 실패할 수 있으며, 그런 경우 Kubernetes는 Pod를 더 이상 복구하려고 시도하지 않습니다.
대신에 k8s는 Pod를 삭제하고 다른 컴포넌트들을 이용하여 자동 복구(automatic healing)를 제공합니다.

Pod가 스케쥴링 된 상태에서 노드가 실패한다면 Pod는 unhealthy 상태로 취급되며 k8s에 의해 삭제됩니다. Pod는 자원 부족이나 노드 유지 보수로 인한 방출(eviction)에서 살아남을 수 없습니다.

## Pod phase
Pod의 `status` 필드는 `PodStatus` 오브젝트입니다. `PodStatus` 오브젝트는 `phase` 필드를 가집니다.
Here are the possible values for `phase`:

| Value       | Description                                                                                                                                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Pending`   | The Pod has been accepted by the Kubernetes cluster, but one or more of the containers has not been set up and made ready to run. This includes time a Pod spends waiting to be scheduled as well as the time spent downloading container images over the network. |
| `Running`   | The Pod has been bound to a node, and all of the containers have been created. At least one container is still running, or is in the process of starting or restarting.                                                                                            |
| `Succeeded` | All containers in the Pod have terminated in success, and will not be restarted.                                                                                                                                                                                   |
| `Failed`    | All containers in the Pod have terminated, and at least one container has terminated in failure. That is, the container either exited with non-zero status or was terminated by the system, and is not set for automatic restarting.                               |
| `Unknown`   | For some reason the state of the Pod could not be obtained. This phase typically occurs due to an error in communicating with the node where the Pod should be running.                                                                                            |

# 참고 자료
Kubernetes Doc
https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
Pod Scheduling Readiness
https://kubernetes.io/docs/concepts/scheduling-eviction/pod-scheduling-readiness/






