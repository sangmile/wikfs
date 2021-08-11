# 시작하기

이 페이지에서는 Amazon EKS 클러스터를 사용하여 AWS Fargate에서 Pod를 실행합니다.

## Fargate pod 실행 역할 생성하기

AWS Fargate에서 Pod를 만들 때 Fargate 인프라에서 실행되는 구성 요소는 사용자 대신 AWS API를 호출합니다. Amazon ECR에서 컨테이너 이미지를 가져오는 것과 같은 작업을 위해 Amazon EKS pod 실행 역할을 생성합니다.

Fargate 프로파일을 생서할 때는 Pod에서 사용할 pod 실행 역할을 지정해야 합니다. 이 역할은 인증을 위해 클러스터의 RBAC에 추가됩니다. 이렇게 하면 Fargate 인프라에서 실행 중인 kubelet이 Amazon EKS 클러스터에 등록되어 있는 클러스터에 노드로 나타날 수 있습니다.

{% hint style="danger" %}
중요  
Fargate에서 실행 중인 Pod 컨테이너는 pod 실행 역할과 연결된 IAM 사용 권한을 사용할 수 없습니다. Fargate Pod의 컨테이너에 다른 AWS 서비스에 액세스할 수 있는 권한을 부여하려면 ServiceAccount에 IAM 역할을 사용해야 합니다. 자세한 내용은 [https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html)에서 확인바랍니다.
{% endhint %}

### 콘솔에서 AWS Fargate pod 실행 역할 생성하기

1. IAM 서비스 클릭
2. Role 생성
3. 서비스에서 EKS 선택 후 use case에서 EKS - Fargate pod를 선택합니다.
4. Role 이름을 `AmazonEKSFargatePodExecutionRole`과 같이 입력합니다.

## Fargate 프로파일 생성하기

Fargate에서 Pod를 스케줄링하려면 먼저 Fargate를 사용하는 pod를 지정하는 Fargate 프로파일을 정의해야 합니다. 자세한 내용은 AWS Fargate 프로파일\([https://docs.aws.amazon.com/eks/latest/userguide/fargate-profile.html](https://docs.aws.amazon.com/eks/latest/userguide/fargate-profile.html)\)을 참조바랍니다.

### 콘솔에서 Fargate 프로파일 생성하기

1. EKS 콘솔 접속 후 Add Fargate Profile 클릭합니다.  


   ![](../../../../../.gitbook/assets/image%20%2816%29.png)

2. Configure Fargate profile 페이지에서는 다음 정보를 입력한 뒤, 다음을 클릭합니다.  
   - Name  
   - Pod execution role  
   - Subnets  
   Subnets에는 기본적으로 모든 Subnet이 선택되어 있습니다. Fargate에서는 Private Subnet만 지원하기 때문에 Private Subnet만 선택해야 합니다.  


   ![](../../../../../.gitbook/assets/image%20%2813%29.png)

3. Configure pod selection 페이지에서는 다음 정보를 입력합니다.  
   - Namespace  
   `kube-system` 또는 `default`와 같은 Fargate에 배포할 네임스페이스를 입력합니다.  
   - Labels  
   특정 네임스페이스에 있는 Pod 중 label을 추가하여 selector와 일치하는 Pod만 배포합니다. `infra: fargate`와 같이 입력합니다.  


   ![](../../../../../.gitbook/assets/image%20%2817%29.png)

## CoreDNS 업데이트하기

기본적으로 CoreDNS는 Amazon EKS 클러스터의 Amazon EC2 인프라에서 실행됩니다. Fargate에서만 실행하려면 아래 주석을 제거해야 합니다.  
eks.amazonaws.com/compute-type: ec2  
CoreDNS Pod를 대상으로 하는 Fargate 프로파일을 생성해야 합니다.

1. CoreDNS Fargate 프로파일 생성 - Name: coredns-fargate - Namespaces / Labels : `kube-system` / `k8s-app: kube-dns`
2. 다음 명령을 입력하여 주석을 제거합니다.

```bash
kubectl patch deployment coredns \
    -n kube-system \
    --type json \
    -p='[{"op": "remove", "path": "/spec/template/metadata/annotations/eks.amazonaws.com~1compute-type"}]'
```

다음 명령을 입력하여 배포를 확인합니다.

```bash
kubectl get po -n kube-system
```

출력은 다음과 같습니다.

```bash
NAME                       READY   STATUS    RESTARTS   AGE
coredns-6b679c7d9b-7m7hj   1/1     Running   0          103s
coredns-6b679c7d9b-sz8hl   1/1     Running   0          103s
```

### Fargate 확인하기

아래 명령을 통해 Fargate 노드를 확인합니다.

```bash
kubectl get nodes
```

출력

```bash
NAME                                                    STATUS     ROLES    AGE   VERSION
fargate-ip-192-168-209-71.us-west-2.compute.internal    Ready      <none>   67m   v1.21.2-13+d2965f0db10712
fargate-ip-192-168-251-107.us-west-2.compute.internal   Ready      <none>   67m   v1.21.2-13+d2965f0db10712
```



