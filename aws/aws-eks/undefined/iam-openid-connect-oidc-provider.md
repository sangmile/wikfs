# IAM OpenID Connect\(OIDC\) Provider

Kubernetes ServiceAccount가 AWS 리소스에 액세스할 수 있도록 클러스터에 대한 OIDC\(ID Connect\) 공급자를 생성합니다. 클러스터에 대해 이 단계를 한 번만 완료하면 됩니다.

1. IAM 서비스 클릭

2. Identity providers 클릭

3. Add Provider 선

![](../../../.gitbook/assets/image%20%288%29.png)

4. Provider type: OpenID Connect 선택

5. Provider URL은 EKS 상세 내용에서 OpenID Connect provider URL 복사하여 붙여 넣은 다음 Get thumbprint를 클릭합니다.

6. Audience에 `sts.amazonaws.com`을 입력하고 Add provider를 클릭합니다.

![OpenID Connect Provider &#xC0DD;&#xC131;](../../../.gitbook/assets/image%20%281%29.png)

![EKS Detail Screenshot](../../../.gitbook/assets/image%20%2812%29.png)

