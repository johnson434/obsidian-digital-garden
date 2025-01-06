---
{"dg-publish":true,"permalink":"/access-in-kubernetes/","dgPassFrontmatter":true}
---

[[03 - Tags/Kubernetes Authentication\|Kubernetes Authentication]]
# 핵심 요약
- k8s의 인증/인가는 kube api-server에서 이뤄진다.
- kube api-server는 REST API를 지원한다.
- `kubectl` 명령어는 내부적으로 REST API 요청으로 변환되어 Control Plane에 요청된다.
- 인증 방식으로 CA 인증서, Token 등 다양한 방식이 있지만 OIDC 확장성과 보안 모두에 이점이 있다.
# 핵심 필기
## Access in Kubernetes
쿠버네티스에서 처음 학습할 때 배우는 내용으로는 Control Plane에 API Server가 있으며 해당 서버에서 인증과 인가를 처리한다는 내용이 있다.
그러나, Minikube나 AWS의 EKS를 사용하면 나도 모르는 사이에 인증/인가 설정이 되어 있는 경우가 부지기수다.
학습 목적으로 minikube를 사용하는 경우, `minikube start`를 통해서 AWS에서 제공하는 매니지드 서비스인 EKS를 사용하는 경우엔, `aws eks update-kubeconfig --region region-code --name my-cluster` 명령어를 통해서 클러스터 인증/인가 설정된다.
이러한 명령어를 통해서 설정된 결과 값은 `cat ~/.kube/config` 명령어를 통해서 확인할 수 있다.
``` bash
~$: cat ~/.kube/config

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: dasdfeRFWNRWnwql...arlkwrnL
    server: https://SECRET.SECRET.ap-northeast-2.eks.amazonaws.com
  name: arn:aws:eks:ap-northeast-2:SECRET:cluster/dev-sp-eks
- cluster:
    certificate-authority: /home/john/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Mon, 06 Jan 2025 14:31:05 KST
        provider: minikube.sigs.k8s.io
        version: v1.34.0
      name: cluster_info
    server: https://172.17.0.2:8443
  name: minikube
contexts:
- context:
    cluster: arn:aws:eks:ap-northeast-2:SECRET:cluster/dev-sp-eks
    namespace: monitoring
    user: arn:aws:eks:ap-northeast-2:SECRET:cluster/dev-sp-eks
  name: arn:aws:eks:ap-northeast-2:SECRET:cluster/dev-sp-eks
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Mon, 06 Jan 2025 14:31:05 KST
        provider: minikube.sigs.k8s.io
        version: v1.34.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: arn:aws:eks:ap-northeast-2:SECRET:cluster/dev-sp-eks
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-northeast-2
      - eks
      - get-token
      - --cluster-name
      - dev-sp-eks
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: minikube
  user:
    client-certificate: /home/john/.minikube/profiles/minikube/client.crt
    client-key: /home/john/.minikube/profiles/minikube/client.key
```

**클러스터 구성**
```
clusters:
- cluster:
    certificate-authority-data: dasdfeRFWNRWnwql...arlkwrnL
    server: https://SECRET.SECRET.ap-northeast-2.eks.amazonaws.com
  name: arn:aws:eks:ap-northeast-2:SECRET:cluster/dev-sp-eks
- cluster:
    certificate-authority: /home/john/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Mon, 06 Jan 2025 14:31:05 KST
        provider: minikube.sigs.k8s.io
        version: v1.34.0
      name: cluster_info
    server: https://172.17.0.2:8443
  name: minikube
```
위의 결과 값에서 clusters 부분을 살펴보면 cluster가 설정되어 있다.
첫 번째 클러스터는 EKS를 통해 구성된 클러스터이며 두 번째 클러스터는 minikube를 통해 로컬에 구성된 클러스터이다. 
두 클러스터는 공통적으로 CA 인증서를 사용하지만 EKS에선 CA 인증서를 `certificate-authority-data`에 평문으로 직접 저장한다. 반면에, Minikube에선 ca 인증서 파일이 저장된 디렉터리를 `certificate-authority`에 명시한다.
또한, 두 클러스터 모두 `server`를 통해서 k8s의 API 서버 엔드포인트를 명시한다. EKS는 AWS에서 만들어주는 Public EndPoint를 통해서 인터넷을 통해 어디서든 접근할 수 있고, minikube의 EndPoint는 Private IP로 설정되어 로컬에서만 접근 가능한 엔드포인트이다.
우리가 실행하는 `kubectl` 커맨드들은 모두 위에 명시한 엔드포인트로 요청이 진행되는 것이다.
아래에선 이를 `curl` 명령어를 통해 REST API 형식에 요청을 보내어 kubectl 명령어를 직접 구현해보겠다

**REST API를 통해 kubectl 명령어 구현**
이번 예제에서는 `kubectl get pods`를 통해서 현재 실행 중인 Pod를 조회하는 REST API를 구현해보겠습니다.
조회에 앞서 예제를 위한 네임스페이스와 deployments를 만들어 보겠습니다.
``` bash
kubectl create ns practice
kubectl create deploy nginx --image=nginx --namespace=practice
```

pod를 만들었으므로 이제 `kubectl get pods -n practice` 명령어를 통해 조회하면 pod가 출력될 것입니다.
``` bash
kubectl get pods -n practice
NAME                     READY   STATUS    RESTARTS   AGE
nginx-676b6c5bbc-qldvr   1/1     Running   0          15s
```

이제 curl을 통해 REST API 형식에 맞춰 API Server에 직접 요청을 보내겠습니다.
``` bash
curl -X GET   --cacert /home/john/.minikube/ca.crt \
	--cert /home/john/.minikube/profiles/minikube/client.crt \
	--key /home/john/.minikube/profiles/minikube/client.key \
	"https://172.17.0.2:8443/api/v1/namespaces/practice/pods" | jq > pod_list.jq
```

`cat` 명령어를 통해서 pod_list.jq를 출력하면 결과는 다음과 같습니다.
``` json
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "4369"
  },
  "items": [
    {
      "metadata": {
        "name": "nginx-676b6c5bbc-qldvr",
        "generateName": "nginx-676b6c5bbc-",
        "namespace": "practice",
        "uid": "76a21ab1-2c18-4067-9ab3-096176c6671a",
        "resourceVersion": "4218",
        "creationTimestamp": "2025-01-06T06:49:41Z",
        "labels": {
          "app": "nginx",
          "pod-template-hash": "676b6c5bbc"
        },
        "ownerReferences": [
          {
            "apiVersion": "apps/v1",
            "kind": "ReplicaSet",
            "name": "nginx-676b6c5bbc",
            "uid": "beddf43a-9850-4669-93cf-4dbd14005874",
            "controller": true,
            "blockOwnerDeletion": true
          }
        ],
        "managedFields": [
          {
            "manager": "kube-controller-manager",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2025-01-06T06:49:41Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:metadata": {
                "f:generateName": {},
                "f:labels": {
                  ".": {},
                  "f:app": {},
                  "f:pod-template-hash": {}
                },
                "f:ownerReferences": {
                  ".": {},
                  "k:{\"uid\":\"beddf43a-9850-4669-93cf-4dbd14005874\"}": {}
                }
              },
              "f:spec": {
                "f:containers": {
                  "k:{\"name\":\"nginx\"}": {
                    ".": {},
                    "f:image": {},
                    "f:imagePullPolicy": {},
                    "f:name": {},
                    "f:resources": {},
                    "f:terminationMessagePath": {},
                    "f:terminationMessagePolicy": {}
                  }
                },
                "f:dnsPolicy": {},
                "f:enableServiceLinks": {},
                "f:restartPolicy": {},
                "f:schedulerName": {},
                "f:securityContext": {},
                "f:terminationGracePeriodSeconds": {}
              }
            }
          },
          {
            "manager": "kubelet",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2025-01-06T06:49:44Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:status": {
                "f:conditions": {
                  "k:{\"type\":\"ContainersReady\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  },
                  "k:{\"type\":\"Initialized\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  },
                  "k:{\"type\":\"PodReadyToStartContainers\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  },
                  "k:{\"type\":\"Ready\"}": {
                    ".": {},
                    "f:lastProbeTime": {},
                    "f:lastTransitionTime": {},
                    "f:status": {},
                    "f:type": {}
                  }
                },
                "f:containerStatuses": {},
                "f:hostIP": {},
                "f:hostIPs": {},
                "f:phase": {},
                "f:podIP": {},
                "f:podIPs": {
                  ".": {},
                  "k:{\"ip\":\"10.244.0.4\"}": {
                    ".": {},
                    "f:ip": {}
                  }
                },
                "f:startTime": {}
              }
            },
            "subresource": "status"
          }
        ]
      },
      "spec": {
        "volumes": [
          {
            "name": "kube-api-access-t4n5n",
            "projected": {
              "sources": [
                {
                  "serviceAccountToken": {
                    "expirationSeconds": 3607,
                    "path": "token"
                  }
                },
                {
                  "configMap": {
                    "name": "kube-root-ca.crt",
                    "items": [
                      {
                        "key": "ca.crt",
                        "path": "ca.crt"
                      }
                    ]
                  }
                },
                {
                  "downwardAPI": {
                    "items": [
                      {
                        "path": "namespace",
                        "fieldRef": {
                          "apiVersion": "v1",
                          "fieldPath": "metadata.namespace"
                        }
                      }
                    ]
                  }
                }
              ],
              "defaultMode": 420
            }
          }
        ],
        "containers": [
          {
            "name": "nginx",
            "image": "nginx",
            "resources": {},
            "volumeMounts": [
              {
                "name": "kube-api-access-t4n5n",
                "readOnly": true,
                "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount"
              }
            ],
            "terminationMessagePath": "/dev/termination-log",
            "terminationMessagePolicy": "File",
            "imagePullPolicy": "Always"
          }
        ],
        "restartPolicy": "Always",
        "terminationGracePeriodSeconds": 30,
        "dnsPolicy": "ClusterFirst",
        "serviceAccountName": "default",
        "serviceAccount": "default",
        "nodeName": "minikube",
        "securityContext": {},
        "schedulerName": "default-scheduler",
        "tolerations": [
          {
            "key": "node.kubernetes.io/not-ready",
            "operator": "Exists",
            "effect": "NoExecute",
            "tolerationSeconds": 300
          },
          {
            "key": "node.kubernetes.io/unreachable",
            "operator": "Exists",
            "effect": "NoExecute",
            "tolerationSeconds": 300
          }
        ],
        "priority": 0,
        "enableServiceLinks": true,
        "preemptionPolicy": "PreemptLowerPriority"
      },
      "status": {
        "phase": "Running",
        "conditions": [
          {
            "type": "PodReadyToStartContainers",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2025-01-06T06:49:44Z"
          },
          {
            "type": "Initialized",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2025-01-06T06:49:41Z"
          },
          {
            "type": "Ready",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2025-01-06T06:49:44Z"
          },
          {
            "type": "ContainersReady",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2025-01-06T06:49:44Z"
          },
          {
            "type": "PodScheduled",
            "status": "True",
            "lastProbeTime": null,
            "lastTransitionTime": "2025-01-06T06:49:41Z"
          }
        ],
        "hostIP": "172.17.0.2",
        "hostIPs": [
          {
            "ip": "172.17.0.2"
          }
        ],
        "podIP": "10.244.0.4",
        "podIPs": [
          {
            "ip": "10.244.0.4"
          }
        ],
        "startTime": "2025-01-06T06:49:41Z",
        "containerStatuses": [
          {
            "name": "nginx",
            "state": {
              "running": {
                "startedAt": "2025-01-06T06:49:44Z"
              }
            },
            "lastState": {},
            "ready": true,
            "restartCount": 0,
            "image": "nginx:latest",
            "imageID": "docker-pullable://nginx@sha256:42e917aaa1b5bb40dd0f6f7f4f857490ac7747d7ef73b391c774a41a8b994f15",
            "containerID": "docker://ddbda02f60eccfb8536bbdafb472c426b391483b51cae26601d850a036e9334b",
            "started": true,
            "volumeMounts": [
              {
                "name": "kube-api-access-t4n5n",
                "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
                "readOnly": true,
                "recursiveReadOnly": "Disabled"
              }
            ]
          }
        ],
        "qosClass": "BestEffort"
      }
    }
  ]
}
```

API Server 서버에는 아래와 같은 방식으로 요청을 처리합니다.
```
allow(request): boolean {
	who := authn(request.headers.authorization)
	what := resource(request.method, request.path)
	return authz(who, what)	
}
```

## Authentication concerns
여러 클러스터를 운영하는 환경에선(이런 환경을 멀티 클러스터라고 칭한다.) 인증을 처리하기 위해 각각의 인증 방식이 필요할 것이다. 만약, 모든 클러스터가 CA 인증 방식을 취한다고 가정해보자.
![Pasted image 20250106162900.png](/img/user/Pasted%20image%2020250106162900.png)

위처럼 클러스터 별로 인증서를 가지게 되면 각 인증서의 유효기간이 만료된 경우에 인증서를 갱신할 필요가 있다. 또한, 클러스터가 추가됐을 때 새로운 인증서 발급이 필요하다. 이처럼 복잡성이 증가한다.

이를 Single Source of Truth 원칙에 맞게 하나의 Authentication Source를 가지는 방법을 가지고 싶을 것이다. 아래와 같이 말이다.
![Pasted image 20250106163433.png](/img/user/Pasted%20image%2020250106163433.png)

### 인증 방법(Authentication Method)
![Pasted image 20241111113915.png](/img/user/image/Pasted%20image%2020241111113915.png)
인증 방식으로 고려되는 방식이 위 그림에 나와있다.
Token file이나 SA Tokens나 인증서 방식은 클라이언트에 누군가 침입했을 때, 탈취 당할 가능성이 높다. 
최근엔 OIDC 인증 방식이 각광 받는 추세다.
## Okta의 OIDC Authentication 흐름

![Pasted image 20241111125532.png](/img/user/image/Pasted%20image%2020241111125532.png)
쿠버네티스 클러스터 내에선 당신이 OIDC를 사용한다는 사실을 알아야하고 OIDC issuer가 누군지 알아야한다.(Issuer를 알아야 Identity 확인이 가능하니까.)
우리는 클러스터 내에 OIDC Application을 만들어야 한다. 또한 사용자가 OIDC를 사용할 수 있도록 클라이언트 마다 설정해줘야 한다.
우리는 이 OIDC 어플리케이션 만들기를 terraform을 통해서 간단하게 설정할 수 있다.

## Kubernetes의 인증 인가 절차
![Pasted image 20241111142050.png](/img/user/image/Pasted%20image%2020241111142050.png)
쿠버네티스는 RBAC(Role Base Access Control)를 통해서 접근을 허용합니다.
'유저/그룹' 또는 ServiceAccount(Pod)에게 Role을 부여하여 User/Group과 ServiceAccount의 API 접근을 제어합니다.
### User/Group/ServiceAccount
특정 행위를 할 주체
사용자는 User, Group에 속하며 Pod는 ServiceAccount에 속한다.
### Role
Role: 사용 가능한 권한을 명시
- 예시) **`default` 네임스페이스의 Pod에 대해 `get`, `watch`, `list` 할 수 있는 권한이 있는 `pod-reader`라는 Role을 생성
``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]  # "" indicates the core API group
  resources: ["pods"]  # resources: ["*"] = 모든 resource를 의미
  verbs: ["get", "watch", "list"]  # verbs: ["*"] = 모든 verb를 의미
```
- `apiGroups`: Pod는 코어 API이기 때문에 apiGroups를 따로 지정하지 않음 ([""] = '코어 API 그룹'을 의미)
- `resources`: "pods", "deployments", "services" 등 사용할 API 리소스들을 명시함
- `verbs`: "create", "get", "update", "delete" 등 허용할 기능을 명시함

```bash
kubectl create role pod-reader --verb=get,watch,list --resource=pods --namespace=default
```

### RoleBinding
- User/Group/ServiceAccount와 Role을 묶는 역할
- Role을 부여하여 API 접근을 허용할 수 있다.
- 예시: 권한이 있는 User/Group이나 ServiceAccount의 접근만을 허용함
```yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef: 
  kind: Role  # this must be Role or ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
	```
- `subjects`: User 'jane'에게 앞서 정의한 'pod-reader'라는 Role을 할당
- `roleRef`: 앞서 정의한 Role을 명세 (apiGroup: rbac api를 사용하기 때문에 rbac.authorization.k8s.io로 설정)
```bash
kubectl create rolebinding read-pods --user=jane --role=pod-reader --namespace=default
```

### ClusterRoleBinding
- RoleBinding은 해당 네임스페이스에서만 사용 가능하지만 ClusterRoleBinding은 클러스터 내 모든 네임스페이스에서 유효하다.
![Pasted image 20241111151821.png](/img/user/image/Pasted%20image%2020241111151821.png)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This cluster role binding allows anyone in the "manager" group to read secrets in any namespace.
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: Group
  name: manager
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

### ClusterRole
- 어떤 API를 이용할 수 있는지 정의하는 것으로 클러스터 전체 네임스페이스에서 유효하다.
``` yaml
# 예시) **전체 네임스페이스의 Secret에 대한 `get`, `watch`, `list` 할 수 있는 권한을 가진 ClusterRole**
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```


# 참고 자료
https://www.youtube.com/watch?v=YqW-R-4HRgE


