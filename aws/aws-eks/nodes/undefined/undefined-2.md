# 노드 IAM role

Amazon EKS 노드 kubelet 데몬은 사용자를 대신하여 AWS API를 호출합니다.노드는 IAM 인스턴스 프로파일을 통해 이러한 API 호출에 대한 사용 권한을 가집니다. 노드를 생성하기 전에 다음 IAM 정책을 사용하여 IAM 역할을 생성해야 합니다.

* AmazonEKSWorkerNodePolicy
* AmazonEC2ContainerRegistryReadOnly

### Amazon EKS 노드 IAM role 생성하기

 AWS 관리 콘솔 또는 AWS CloudFormation을 사용하여 노드 IAM role을 생성할 수 있습니다. AWS CloudFormation을 통해 생성하겠습니다.

1. CloudFormation 서비스에 접근합니다.
2. 스택 생성을 클릭합니다.
3. 다음 정보를 입력합니다. - Amazon S3 URL에는 [`https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-nodegroup-role.yaml`](https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-nodegroup-role.yaml) 을 입력합니다. - Stack name에는 `eks-node-group-instance-role`과 같이 입력합니다.
4. 스택을 생성합니다.

{% hint style="info" %}
권장  
위에서 CloudFormation으로 생성한 IAM role에는 AmazonEKS\_CNI\_Policy 정책이 포함되어 있습니다. CNI 플러그인에 사용되는 aws-node ServiceAccount에 이 정책을 할당하는 것이 좋습니다.
{% endhint %}

![CloudFormation&#xC73C;&#xB85C; &#xC0DD;&#xC131;&#xD55C; IAM role&#xC5D0; &#xBD80;&#xC5EC;&#xB41C; policy](../../../../.gitbook/assets/image%20%2818%29.png)

