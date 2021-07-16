# kubeadm 설치 문서
## 목차
### 1. kubeadm 으로 클러스터 배포
### 2. 노드 추가 
### 3. 버전 업그레이드
### 4. 애드온

<br>
<br>

___
## 1. kubeadm 으로 클러스터 배포 
#### 참고 문서 https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
#### 참고 문서 https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
</br>


> ### kubeadm 설치


1) apt 패키지 색인을 업데이트 

 <code> $ sudo apt-get update </code>
 
  
2) 쿠버네티스 apt 리포지터리를 사용하는 데 필요한 패키지를 설치
  
 <code> $ sudo apt-get install -y apt-transport-https ca-certificates curl </code>
 
   
3) 구글 클라우드의 공개 사이닝 키를 다운로드 
   
<code> $ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg </code>


4) 쿠버네티스 apt 리포지터리를 추가

<code>$  echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list </code>


5) apt 패키지 색인을 업데이트하고, kubelet, kubeadm, kubectl을 설치하고 해당 버전을 고정

- kubeadm: 클러스터를 부트스트랩하는 명령이다
- kubelet: 클러스터의 모든 머신에서 실행되는 파드와 컨테이너 시작과 같은 작업을 수행하는 컴포넌트이다
- kubectl: 클러스터와 통신하기 위한 커맨드 라인 유틸리티이다

<code>$ sudo apt-get update </code>

<code>$ sudo apt-get install -y kubelet kubeadm kubectl </code>

<code>$ sudo apt-mark hold kubelet kubeadm kubectl </code>


<br>
<br>

>### Cluster 배포 <br>

>> #### control-plane 설정 <br>

1) init <br>

<code>$ kubeadm init —control-plane-endpoint <IP주소> --pod-network-cidr <IP 대역> --apiserver-advertise-address <IP 사용 대역> </code><br>

2) 자격증명 <br>
  
<code> $ mkdir -p $HOME/.kube </code><br>
 
<code> $ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config </code><br>
 
<code> $ sudo chown $(id -u):$(id -g) $HOME/.kube/config </code><br>

3) pod 네트워크 add-on 설치 
  
  <code>$ kubectl apply -f https://www.projectcalico.org/manifests/calico.yaml </code>
<br>
<br>
<br>

___  
  
## 2. node 추가 

>> ### node를 클러스터에 추가 (join)
  
control-plane을 init 할 때 명령어 확인 가능 <br>

<code>$ kubeadm join --token <token> <control-plane-host>:<control-plane-port> --discovery-token-ca-cert-hash sha256:<hash> </code>

  >>>
- token 을 모를 때, 토큰 확인 <br>
 
    <code>$ kubeadm token list </code> <br>
만약 토큰이 없다면 <code>$ kubeadm token create </code> 로 생성하여 확인 
  
 
- hash 를 모를 때, 해시 확인 <br>
 
  <code>$ openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \
  openssl dgst -sha256 -hex | sed 's/^.* //' </code> 명령어로 확인 
  
  
 <br>
  <br>
  
  
  ___
  
## 3. 버전 업그레이드
#### 참고 문서 https://kubernetes.io/ko/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
  
> ### control-plane 업그레이드
>> #### kubeadm 업그레이드

1. kubeadm 업그레이드<br>
 
  <code>$ apt-get update </code><br>
 
  <code>$ apt-get install -y --allow-change-held-packages kubeadm=<원하는버전></code>
 
    
2. 다운로드하려는 버전이 잘 받아졌는지 확인<br>
   
  <code>$ kubeadm version</code>

    
3. 업그레이드 계획을 확인 <br>
   
  <code>$ kubeadm upgrade plan</code>
  
    
4. 패치 버전 바꾸기<br>
   
  <code>$ sudo kubeadm upgrade apply <원하는 버전></code>
  <br>
    <br>
   
>> #### kubelet과 kubectl 업그레이드
 
1. kubelet과 kubectl 업그레이드<br>
     
  <code>$ apt-get update</code><br>
     
  <code> $ apt-get install -y --allow-change-held-packages kubelet=<원하는버전> kubectl=<원하는버전></code><br>

   
2.  kubelet을 다시 시작<br>
  
   <code>$ sudo systemctl daemon-reload</code><br>
  
   <code>$ sudo systemctl restart kubelet</code>

   
   
<br>
   
   
<br>

   
   
> ### node 업그레이드
>> #### kubeadm 업그레이드  
   
1. kubeadm 업그레이드<br>
  
  <code>$ apt-get update </code><br>
  
  <code>$ apt-get install -y --allow-change-held-packages kubeadm=<원하는버전></code>
 

2. 로컬 kubelet 구성을 업그레이드
   
   <code>$ sudo kubeadm upgrade node</code>
    <br>
    <br>
    
>> #### kubelet과 kubectl 업그레이드
 
1. kubelet과 kubectl 업그레이드<br>
   
  <code>$ apt-get update</code><br>
   
  <code>$ apt-get install -y --allow-change-held-packages kubelet=<원하는버전> kubectl=<원하는버전></code><br>

   
2.  kubelet을 다시 시작<br>
  
   <code>$ sudo systemctl daemon-reload</code><br>
  
   <code>$ sudo systemctl restart kubelet</code>    
   


   
<br>

 <br>
   
___
## 4. 애드온
쿠버네티스가 제공하지 못하는 기능을 추가할때 사용  
   <br>
   
> ### MetalLB 
#### 참고 문서 https://metallb.universe.tf/installation/ <br>
#### 참고 문서 https://metallb.universe.tf/configuration/

1. MetalLB 설치를 위해 manifest허용<br>
  
<code>$ kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/namespace.yaml </code><br>
  
<code>$ kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/metallb.yaml</code><br>
   
2. MetalLB 생성 <br>
  
   <code>$ mkdir -p addon/metallb</code><br>
  
   <code>$ cd addon/metalld</code><br>
   <br>
   
   
   
 ```
      
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.1.240-192.168.1.250 (로드밸런스로 사용 할 주소 )
```
   
   
   <br>
   
   
   3. MetalLB 리소스 생성 <br>
   
   <code>$ kubectl create -f metallb-config.yaml </code>
   
   <br>
   
   4. 서비스 생성
   
   ```
   metallb-svc-lb.yaml
   
   apiVersion: v1
   kind: Service
   metadata:
     name: myapp-svc-lb
   spec:
     type: LoadBalancer
     ports:
     - port: 80
       targetPort: 80
     selector:
       app: myapp-rs-web (연결할 앱 선택)
   
   ```
   
  <code>$ kubectl create -f metallb-svc-lb.yaml</code><br>
   
   
   

<br> 
<br>
  

    
    
> ### Ingress
#### 참고 문서 https://kubernetes.github.io/ingress-nginx/deploy/
#### 참고 문서 https://kubernetes.io/ko/docs/concepts/services-networking/ingress/
  
     
   

 
1. Nginx-ingress controller 다운로드 <br> 
  
<code>$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.48.1/deploy/static/provider/aws/deploy.yaml</code><br>
     

2. Ingress 생성     


```
ingress.yaml

apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: myapp-ing
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: myapp-svc-np
          servicePort: 80
     
```
 
 <br>   
     
 <code>$ kubectl apply -f ingress.yaml --namespace=ingress-nginx </code>
  
  
 <br>
  <br>
  
  
  
> ### rook
  #### 참고 문서 https://rook.io/docs/rook/v1.6/ceph-quickstart.html

>> 1. ceph cluster 생성
  
 
1. Ceph 소스 다운로드<br>
  
     <code>$ git clone --single-branch --branch v1.6.7 https://github.com/rook/rook.git</code><br>
  
     <code>$ cd rook/cluster/examples/kubernetes/ceph</code>


2. crds, common, operator 리소스 생성<br>
  
     <code>$ kubectl create -f crds.yaml -f common.yaml -f operator.yaml</code>

3. cluster 생성<br>
  
     <code>$ kubectl create -f cluster.yaml </code> (다중)<br>

     <code>$ kubectl create -f cluster-test.yaml </code> (단일)
  <br>
  
  
>>2.블록 스토리지 생성  <br>
     
     <code> kubectl create -f csi/rbd/storageclass.yaml </code><br>
  <br>
  
     
>>3.파일 스토리지 생성
     
1. 파일 시스템 생성<br>
  
  <code>$ kubectl create -f filesystem.yaml</code> (다중)<br>

  <code>$ kubectl create -f filesystem-test.yaml</code> (단일)<br>
  <br>
  
<br>  


> ### metrics-server
#### 참고 문서 https://kubernetes.io/ko/docs/tasks/debug-application-cluster/resource-metrics-pipeline/
  
1. metrics-server 파일 받기<br>
  
  <code>git clone https://github.com/kubernetes-incubator/metrics-server.git</code><br>
  
  <code>cd metrics-server</code>

 2. Deployment를 생성<br>
  
  <code>cd metrics-server</code><br>
  
  <code>kubectl apply -f deploy/1.8+/</code>
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
