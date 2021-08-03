# Credentials 옵션

## AWS Credential 옵션이란?

AWS Cloud9이 생성하는 임시 AWS 액세스 자격 증명으로 Cloud9을 생성한 IAM User의 권한을 가지고 있습니다.

### 자격 증명 확인

다음 명령어를 사용하여 현재 AWS Cloud9이 어떤 자격 증명을 가지고 있는지 확인할 수 있습니다.

```bash
aws sts get-caller-identity
```

출력은 다음과 같습니다.

```bash
{
    "Account": "<ACCOUNTID>", 
    "UserId": "USERID", 
    "Arn": "arn:aws:iam::<ACCOUNTID>:user/<IAMUSERNAME>"
}
```

### 활성화/비활성화

기본적으로 활성화가 되어 있습니다. 활성화시 위 자격 증명을 갖게 되고 비활성화시 인스턴스 프로파일이 사용자를 대신하여 임시 자격 증명을 관리합니다.

 설정 위치는 다음과 같습니다.  
1. Cloud9에서 Preference\(톱니바퀴\) 클릭

![](../../.gitbook/assets/image%20%282%29.png)

2. AWS Settings에서 Credentials에서 활성화/비활성화합니다.

![](../../.gitbook/assets/image%20%2811%29.png)

비활성화시 Cloud9 EC2에 연결된 IAM Role이 임시 자격 증명을 관리합니다.  
비활성화 후 임시 자격 증명을 확인해보면 다음과 같이 출력됩니다.

```bash
{
    "Account": "<ACCOUNTID>", 
    "UserId": "<USERID>:<INSTANCEID>", 
    "Arn": "arn:aws:sts::<ACCOUNTID>:assumed-role/<IAMROLE>/<INSTANCEID>"
}
```

