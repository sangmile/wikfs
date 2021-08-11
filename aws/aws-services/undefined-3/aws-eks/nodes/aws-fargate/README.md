# AWS Fargate

Amazon EKS를 사용하여 AWS Fargate에 Pod를 배포해보도록 하겠습니다.

### What is Fargate?

AWS Fargate는 컨테이너에 맞는 On-demand 컴퓨팅 용량을 제공하는 기술입니다. AWS Fargate를 사용하면 가상 머신을 프로비저닝, 구성 또는 확장할 필요가 없습니다. 서버 유형을 선택하거나 노드 그룹의 크기를 조정하거나 클러스터 패킹을 최적화할 필요가 없습니다.

### Considerations

Amazon EKS에서 Fargate를 사용할 때 다음 사항들을 고려해야 합니다.

* Fargate에서 실행되는 각 Pod는 자체 분리 경계를 가지며 기본 커널, CPU 리소스, 메모리 리소스 또는 ENI를 다른 Pod와 공유하지 않습니다.
* 네트워크 로드 밸런서 및 애플리케이션 로드 밸런서는 IP 대상이 있는 Fargate에서만 사용할 수 있습니다. 자세한 내용은 Amazon EKS에서 네트워크 로드 밸런서 생성\([https://docs.aws.amazon.com/eks/latest/userguide/network-load-balancing.html\#network-load-balancer](https://docs.aws.amazon.com/eks/latest/userguide/network-load-balancing.html#network-load-balancer)\) 및 애플리케이션 로드 밸런싱\([https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html](https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html)\)을 참조하십시오.
* Pod는 Fargate 프로파일과 일치해야 Fargate에서 실행 가능합니다.
* Security Group을 사용할 수 있는 버전은 1.19 또는 1.20 버전 이상입니다.
* Fargate에서는 DaemonSet을 지원하지 않습니다. 애플리케이션에 DeamonSet이 필요한 경우 파드에서 사이드카 컨테이너로 실행되도록 재구성해야 합니다.
* Fargate에서 실행 중인 파드는 파드 매니페스트에서 HostPort 또는 HostNetwork를 지정할 수 없습니다.
* 기본 nofile 및 nproc 소프트 리밋은 1024이고 Fargate 파드의 경우 하드 리밋은 65535입니다.
* Fargate에서 실행되는 Pod는 Private Subnet만 지원됩니다. Internet Gateway에 대한 직접 경로가 제공되지 않으므로 NAT Gateway로 액세스해야 합니다.
* VPA\(Vertical Pod Autoscaler\)를 사용하여 처음에는 Fargate 파드의 CPU 및 메모리 크기를 적절하게 조정한 다음 HPA를 사용하여 확장합니다. VPA를 활성화하려면 VPA 모드를 Auto 또는 Recreate 모드로 설정합니다. 자세한 내용은 GitHub에서 Vertical Pod Autoscaler 설명서\([https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler\#quick-start](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#quick-start)\)를 참조바랍니다.
* VPC에서 DNS resolution 및 DNS hostnames를 사용하도록 설정해야 합니다.
* Fargate는 VM이 분리된 환경에서 다른 Pod와 리소스를 공유하지 않고 각 Pod를 실행합니다. 그러나 쿠버네티스는 single-tenant 오케스트레이터이기 때문에 Fargate는 Pod 수준 보안 격리를 보장할 수 없습니다. 중요한 워크로드를 별도의 Amazon EKS 클러스터를 사용하여 완벽한 보안 분리가 필요합니다.
* Fargate 프로파일은 보조 CIDR Block의 서브넷 지정을 지원합니다. 자세한 내용은 VPC에 IPv4 CIDR 블록 추가\([https://docs.aws.amazon.com/vpc/latest/userguide/VPC\_Subnets.html\#vpc-resize](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize)\)를 참조하십시오.
* Fargate 노드에 배포된 Pod에서 IMDS\(Amazon EC2 Instance Metadata Service\)를 사용할 수 없습니다. IAM 자격 증명이 필요하면 서비스 계정에 대한 IAM 역할을 사용하여 Pod에 할당해야 합니다. IMDS를 통해 제공되는 정보가 필요하면 하드 코딩해야 합니다.



