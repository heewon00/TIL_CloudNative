## 🎀 Upgrading K8S Clusters

### k8s 웹 실습
- 🔗[Play with Kubernetes](https://labs.play-with-k8s.com/) : CentOS 환경<br>


### 🚀 실습 (CentOS기준)
- 🔗[Upgrading kubeadm clusters](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

- 초기 설정
    1. Initializes cluster master node
        `kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16`
        - 이 때 `kubeadm join`명령어 복사해놓기 (노드 join시 필요)
    2. Initialize cluster networking:
        `kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml`
    
- kubeadm upgrade
    1. 릴리즈 된 버전 확인<br>
    `yum list --showduplicates kubeadm --disableexcludes=kubernetes`
    2. kubeadm 패키지 설치<br>
    `yum install -y kubeadm-'1.28.1-0' --disableexcludes=kubernetes`
    3. kubdeam 패키지 버전 확인<br>
    `kubeadm version`<br>
    4. 현재 버전과 업그레이드되는 버전 확인<br>
    `kubeadm upgrade plan`<br>
    5. kubeadm upgrade<br>
    `kubeadm upgrade apply v1.28.1`<br>
    * kubeadm이 업그레이드 될 뿐 Kubelet, Kubectl은 업그레이드 되지 않았음
    
- control plane upgrade
    1. Drain the node<br>
    `kubectl drain <node-to-drain> --ignore-daemonsets`<br>
    `kubectl drain node1 --ignore-daemonsets`
    2. Upgrade kubelet and kubectl<br>
    `yum install -y kubelet-'1.28.1-0*' kubectl-'1.28.1-0*' --disableexcludes=kubernetes`
    3. restart<br>
    `systemctl daemon-reload`<br>
    `systemctl restart kubelet`
    4. Uncordon the node<br>
    `kubectl uncordon <node-to-uncordon>`<br>
    `kubectl uncordon node1`
    5. 결과 확인
    
- worker node upgrade
    - 🔗 [쿠버네티스 kubeadm을 활용한 클러스터 업그레이드](https://velog.io/@_zero_/쿠버네티스-kubeadm을-활용한-클러스터-업그레이드)
    - 새로운 노드 추가하고 `kubeadm join`명령어 입력→ `kubectl get node` 통해 생성 확인
    1. `ssh <node-to-upgrade>`
    2. 이전 과정 그대로 반복
    3. `exit`하고 `k get node`로 업데이트 확인

### 🚀 Kodekloud 문제
- How many nodes can host workloads in this cluster?
  - `k describe node` 했을 때 Taints 가 없으므로 모든 노드 호스트 가능
  <img width="471" alt="image" src="https://github.com/heewon00/TIL_K8S/assets/55778040/7cc85710-01b2-476c-b03c-823927add255">
