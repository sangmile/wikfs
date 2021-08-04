# 유저 관리

Amazon EKS 클러스터를 생성하면 클러스터를 생성한 통합 사용자와 같은 IAM 사용자 또는 역할에 control plane에서 클러스터의 RBAC 구성에 system:masters 권한이 자동으로 부여됩니다. 이 IAM 개체는 ConfigMap 또는 표시되는 다른 구성에 나타나지 않으므로 클러스터를 처음 생성한 IAM 개체를 알고 있어야 합니다. AWS 사용자 또는 역할에 클러스터와 상호 작용할 수 있는 기능을 부여하려면 Kubernetes 내에서 aws-auth ConfigMap을 편집해야 합니다.







