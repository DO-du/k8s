# kubeadm 설치 문서 
## 목차
1. kubeadm 으로 클러스터 배포
2. 버전 업그레이드
3. 노드 추가
4. 애드온

## 1. kubeadm 으로 클러스터 배포 
#### 참고 문서 https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
#### 참고 문서 https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

### kubeadm 설치


1) apt 패키지 색인을 업데이트 

 <code> sudo apt-get update </code>
  
2) 쿠버네티스 apt 리포지터리를 사용하는 데 필요한 패키지를 설치
  
 <code> sudo apt-get install -y apt-transport-https ca-certificates curl </code>
   
3) 구글 클라우드의 공개 사이닝 키를 다운로드 
   
<code> sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg </code>

4) 쿠버네티스 apt 리포지터리를 추가

<code> echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list </code>

5) apt 패키지 색인을 업데이트하고, kubelet, kubeadm, kubectl을 설치하고 해당 버전을 고정

<code> sudo apt-get update </code>
<code> sudo apt-get install -y kubelet kubeadm kubectl </code>
<code> sudo apt-mark hold kubelet kubeadm kubectl </code>

### 클러스터 배포

1)
<code> mkdir -p $HOME/.kube </code>

