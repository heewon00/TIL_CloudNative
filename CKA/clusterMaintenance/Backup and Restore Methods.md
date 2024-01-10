# 🎀 Backup and Restore Methods

## 🐱 모든 네임스페이스의 모든 리소스 설정을 백업

`kubectl get all -A -o yaml > backup.yaml`
    
## 🐱 ETCD 백업
### 🚀 환경변수 등록 : `export ETCDCTL_API=3`
- etcdctl은 etcd API 버전 3을 사용하도록 지시함
### 🚀 파드의 설정 정보 확인
- 기본 정보 확인 : `kubectl describe po [etcd_파드이름] -o yaml -n kube-system`
    - ETCD Pod는 kube-system ns의 etcd-controlplane pod에 저장됨
- etcd 파일 세부 정보 확인 : `/etc/kubernetes/manifests/etcd.yaml`

            
### 🚀 스냅샷 저장
- `etcdctl snapshot save [스냅샷_저장경로]
--endpoints=[endpoint-url] \
--cacert=[trusted-ca-file] \
--cert=[cert-file] \
--key=[key-file]`
- ex) `etcdctl snapshot save /opt/snapshot-pre-boot.db
--endpoints=https://127.0.0.1:2379
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key`
    - endpoint: 2379포트를 이용해 etcd 연결 중임을 뜻함
        - etcd 파일의 —listen—client-urls에서 확인 가능
    - TLS(전송계층보안) 연결 인증을 위한 옵션으로, **etcd 서버 자체의 인증 및 보안에 사용됨**
        - cacert: 인증 기관(Certificate Authority)의 인증서
            - **클라이언트(etcdctl)가 서버(etcd서버)** 인증서를 **신뢰할 수 있는지 확인**하는데 사용됨
        - cert: 서버 또는 클라이언트 자체의 **신원을 확인**하는데에 사용
            - 서버 또는 클라이언트가 자신을 식별하고 다른 측에게 자신이 누구인지 알림
        - key: 인증서와 연결된 개인 키
### 🚀 스냅샷 복원
1. kube-apiserver 중단 : `service kube-apiserver stop`
    - 복원 프로세스는 etcd 클러스터를 재시작해야하는데 kube-apiserver가 의존성이 있기 때문에 중단해야 함
2. 스냅샷 복원
    - `etcdctl snapshot restore [스냅샷_저장경로] \
    ~~--cacert=[trusted-ca-file] \
    --cert=[cert-file] --key=[key-file] \~~
    --data-dir=[변경할_데이터_디렉터리_경로]`
        - ex) `etcdctl snapshot restore snapshot.db —data-dir /var/lib/etcd-from-backup`
    - 기존 경로에 덮어씌우기 하면 적용 안 됨… <br>
    --data-dir 옵션 써서 새 폴더에 snapshot복원할 것
3. `/etc/kubernetes/manifests/etcd.yaml` 에서 hostpath 변경
    <details>
    <summary>(참고) 볼륨 개념</summary>
    <div markdown="1">

    - 볼륨을 마운트하는 이유
        - 컨테이너는 일시적이며, 컨테이너가 종료되면 내부 데이터는 모두 삭제됩니다. 이것은 컨테이너의 가변적인 특성 때문에 발생하는 문제 중 하나입니다.
        - 볼륨을 마운트하면 컨테이너의 **가상 파일 시스템에 외부 저장소(호스트의 파일 시스템, 네트워크 스토리지 등)를 연결**하여 **컨테이너가 영구적인 데이터를 저장하고 유지**할 수 있습니다. 이는 데이터의 보존과 지속성을 제공하며, 컨테이너가 종료되어도 데이터가 유실되지 않게 해줍니다.
        - 또한, **여러 컨테이너 간에 데이터를 공유하거나, 데이터베이스나 파일 시스템과 같은 영구 데이터를 사용**하기 위해서도 볼륨을 마운트하는 것이 필요합니다.
        - 실제로 파일을 복사하는 것이 아니라, 해당 경로를 연결하여 호스트의 데이터에 접근할 수 있도록 만드는 것이 마운트의 역할입니다. **컨테이너가 `mountPath`에 있는 데이터에 접근하면, 실제로는 호스트의 `hostPath` 경로에 있는 데이터를 참조**하게 됩니다.
    
    ```yaml
    volumeMounts:
        - mountPath: /var/lib/etcd
            name: etcd-data
        - mountPath: /etc/kubernetes/pki/etcd
            name: etcd-certs
    .
    .
    .
        volumes:
        - hostPath:
            path: /etc/kubernetes/pki/etcd
            type: DirectoryOrCreate
        name: etcd-certs
        - hostPath:
            path: /var/lib/etcd-new
            type: DirectoryOrCreate
        name: etcd-data
    ```
    
    - volumeMounts : 실제 컨테이너의 파일 시스템에 마운트 됨
        - **mountPath: Pod 컨테이너 내부 경로**
            - 컨테이너 내부의 해당 경로에 호스트의 파일 시스템을 마운트
        - volumes
            - emptyDir: 파드 단위로 마운트되는 볼륨
                - 최초 생성될 때 볼륨 내용이 비어있음
                - 파드 생성시 만들어지고 삭제시 없어지므로 일시적인 데이터 보관
            - **hostPath: Node 내부 경로**
                - 호스트 머신의 파일 시스템 경로를 지정
                - 워커 노드의 파일 시스템을 파드의 디렉터리로 마운트하는데 사용
                - Pod가 죽어도 볼륨 데이터는 사라지지 않음
    </div>
    </details>

4. `systemctl daemon-reload`
5. `service etcd restart`
6. `service kube-apiserver start`
## 🐱 클러스터
- 클러스터에 대한 현재 설정을 보여주는 명령어 : `kubectl config view`
- 클러스터 변경 : `kubectl config use-context <cluster이름>`