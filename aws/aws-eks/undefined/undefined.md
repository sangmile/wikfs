# 유저 관리

Amazon EKS 클러스터를 생성하면 클러스터를 생성한 통합 사용자와 같은 IAM 사용자 또는 역할에 control plane에서 클러스터의 RBAC 구성에 `system:masters` 권한이 자동으로 부여됩니다. 이 IAM 개체는 ConfigMap에 나타나지 않으므 클러스터를 처음 생성한 IAM 개체를 알고 있어야 합니다. AWS 사용자 또는 역할에 클러스터와 상호 작용할 수 있는 기능을 부여하려면 Kubernetes 내에서 `aws-auth` ConfigMap을 편집해야 합니다.

## Amazon EKS 클러스터에 IAM user 또는 role 추가

위에서 언급했듯이 클러스터를 최초 구성한 사용자가 해당 작업을 진행해야 합니다.

아래 명령을 통해 AWS 자격 증명이 클러스터에 대해 kubectl 명령 권한이 있는지 검증합니다.

```text
kubectl get svc
```

 출력:

```text
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   29h
```

 aws-auth ConfigMap을 편합니다.

```text
kubectl edit cm aws-auth -n kube-system
```

 출력:

```yaml
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
data:
  mapRoles: |
    - groups:
      - system:bootstrappers
      - system:nodes
      - system:node-proxier
      rolearn: arn:aws:iam::<ACCOUNTID>:role/AmazonEKSFargatePodExecutionRole
      username: system:node:{{SessionName}}
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::<ACCOUNTID>:role/eks-node-group-instance-role-NodeInstanceRole-1K4W0K6FEGRR3
      username: system:node:{{EC2PrivateDNSName}}
kind: ConfigMap
metadata:
  creationTimestamp: "2021-08-04T04:33:41Z"
  name: aws-auth
  namespace: kube-system
  resourceVersion: "224664"
  uid: ...
```

 "`Error from server (NotFound): configmaps "aws-auth" not found`" 에러 발생 시 아래 URL을 참고바랍니다.  
[https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html)

아래와 같이 IAM user를 추가합니다.

```yaml
...
data:
  mapRoles: |
    - groups:
    ...
  mapUsers: |
    - userarn: <arn:aws:iam::111122223333:user/admin>
      username: <NAME>
      groups:
        - system:masters
...
```

 출력:

```yaml
configmap/aws-auth edited
```

 위에서 추가한 IAM User로 kubectl 명령을 통해 클러스터에 자격 증명을 합니다.

```yaml
aws sts get-caller-identity
```

 출력에서 볼 수 있듯이 위에서 추가한 userarn과 동일한 사용자 테스트를 합니다.

```yaml
{
    "Account": "111122223333", 
    "UserId": "USERID", 
    "Arn": "arn:aws:iam::111122223333:user/admin"
}
```

 kubectl 명령을 통해 자격 증명이 되었는지 확인합니다.

```yaml
aws eks update-kubeconfig --name <clustername> --region <regionname>
kubectl get svc
```

 다음과 같이 출력되어야 합니다.

```yaml

```

