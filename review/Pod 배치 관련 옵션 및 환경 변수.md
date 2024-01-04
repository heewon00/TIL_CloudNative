# 💜 복습

## 💦 Pod 배치 관련 헷갈리는 옵션 
### 1. nodeName
- kube-scheduler의 영향을 받지 않고 특정 노드에 강제로 배치
### 2. Taints & Tolerations
- Taints: 특정 조건을 가진 Pod가 특정 노드에 할당되는 것을 방지
  - master node에도 Taint가 설정되어 있다.
- Tolerations: 특정 Taint를 용인할 수 있는 Toleration 설정을 가진 Pod는 해당 Node에 할당 가능함 
### 3. Node Affinity
- Pod를 배치함에 있어서 선호하는 Node를 설정할 수 있게끔 하는 리소스
### 4. Drain
- 노드에 존재하는 모든 Pod를 제거하여 노드를 비우고, 기존 Pod들을 다른 노드에 스케줄링하는 명령어
  - 단, kube-apiserver, kube-proxy와 같은 Pod들을 Static Pod이기 때문에 계속 남아있음
  - 따라서 StandAlone Pod는 다른 노드에 배포되지 않고 그냥 사라짐
  - ReplicaSet, DaemonSet, StatefulSet 등에 의해 관리되는 Pod만 새로운 노드에 스케줄링 됨
### 5. Cordon
- 현재 노드에 배포된 Pod은 그대로 유지하면서, 추가적인 Pod의 배포를 제한하는 명령어
<br>
<br>

## ⚙️ 환경 변수 설정
### 1. 환경 변수
- Pod에 환경 변수를 지정하거나, 데이터와 설정 등을 저장함
### 2. ConfigMaps
- 정의 파일이 많을 경우 환경 변수가 어려워짐. 이 때 ConfigMap으로 관리함
### 3. Secrets
- 비밀번호, OAuth 토큰, ssh 키 같은 민감 정보들을 저장하는 용도로 사용
- 시크릿의 경우 ConfigMap에서 입력한 값이 base64로 인코딩되어 저장
  - 인코딩만 되어있기 때문에 누구나 기밀 문서로 만든 파일을 볼 수 있고 비밀 개체를 볼 수 있음 (Not Encrypted. Only Encoded)
  - 따라서 같은 ns에서 pods와 deployments를 생성할 수 있으면 시크릿에 접근할 수 있음
- 해결 방법
    - 시크릿에 저장된 데이터 암호화 고려 (Encrypting Confidential Data at Rest)
    - RBAC를 통해 액세스 제한 고려
    - 특정 컨테이너만 시크릿에 접근 가능하게 한다
    - Third-party secrets store providers 고려 (AWS Provider, Azure Provider 등)
        