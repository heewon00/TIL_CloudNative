## 🐱  원격 노드**에서 ETCD 백업하기**

### 🚀 클러스터
- 클러스터에 대한 현재 설정을 보여주는 명령어 : `kubectl config view`
- 클러스터 변경 : `kubectl config use-context <cluster이름>`
    - **`kubectl`** 명령을 실행할 때 사용할 클러스터, 네임스페이스 및 사용자 인증 정보 등을 설정하는 것입니다. 이 명령을 사용하면 **`kubectl`** 명령어가 특정한 클러스터에서 작업하도록 설정됩니다.
### 🚀 Stacked etcd, External etcd
- Stacked etcd vs External etcd
    - stacked etcd는 **control plane node에서 etcd가 작동**
        
    - external etcd는 **control plane node가 아닌 다른 노드에서 작동**
        
        
- Stacked etcd vs External etcd 확인하기 : `k describe pod kube-apiserver-cluster2-controlplane -n kube-system`
    - Stacked etcd
        - 로컬호스트 주소 사용
            
    - External etcd
        - 외부 주소 사용
                
### 🚀 **etcd 스냅샷 저장**
- 1) 클러스터 변경 : `kubectl config use-context cluster1`
- 2) 원격 노드 접속 : `**ssh cluster1-controlplane**`
    - `ps -ef` : 리눅스나 유닉스 기반 시스템에서 실행되는 프로세스를 보여주는 명령어입니다.
        - **`e`**: 모든 사용자의 프로세스를 출력합니다.
        - **`f`**: 상세한 정보를 포함하여 프로세스를 출력합니다. 이 옵션을 사용하면 명령어 실행 시 사용된 옵션과 함께 각 프로세스의 상세 정보(UID, PID, PPID, CPU 사용량, 메모리 사용량, 시작 시간 등)를 보여줍니다.
        
    - etcd 멤버 목록 확인
        - `ETCDCTL_API=3`
        - `etcdctl member list --endpoints [https://127.0.0.1:2379](https://127.0.0.1:2379/) --cacert /etc/etcd/pki/ca.pem --cert /etc/etcd/pki/etcd.pem --key /etc/etcd/pki/etcd-key.pem`
            
            
- 3) 백업 : `ETCDCTL_API=3 etcdctl snapshot save /opt/cluster1.db --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key`
- 4) 원격 노드 exit 후 `scp cluster1-controlplane:/opt/cluster1.db /opt/` 로 로컬 노드에 복사하기
### 🚀 **etcd 스냅샷 백업**
- 로컬노드에 있는 cluster.db스냅샷을 etcd로 이동시키기
    
    - `scp /opt/cluster2.db etcd-server:/root`
- `ssh etcd-server` 해서 파일 확인
- `etcdctl snapshot restore cluster2.db --data-dir /var/lib/etcd-data-new`
- 권한 확인 해보면 root 사용자로 설정되어 있음 → 소유자 변경하기
    
    - `chown -R etcd:etcd etcd-data-new/`
        - `etcd:etcd`는 소유자와 그룹을 나타내는데, 첫 번째`etcd`는 소유자의 이름이고, 두 번째 `etcd`는 해당 파일이나 디렉토리의 그룹을 나타냅니다.
        - 이 명령어를 통해 `etcd-data-new/` 디렉토리와 그 안에 있는 모든 파일과 디렉토리의 소유자와 그룹을 `etcd:etcd`로 변경하게 됩니다.
        - 예를 들어, `etcd-data-new/` 디렉토리와 그 안의 파일들이 특정 유저 `etcd`와 해당 그룹에 속한 유저들에게 소유권이 있도록 설정하는 것과 비슷합니다. 이는 특정 프로세스나 사용자가 해당 디렉토리나 파일에 접근할 수 있도록 권한을 부여하는 데 사용될 수 있습니다.
- `vi /etc/systemd/system/etcd.service` 에서 data directory path변경
    
    
- `systemctl daemon-reload`
- `exit`