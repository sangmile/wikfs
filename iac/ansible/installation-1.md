# Installation

## 설치 및 설정

## 컨트롤 노드

### Ansible 설치

```bash
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

### Ansible 설정

프로젝트 폴더 생성

```bash
mkdir example-project && cd example-project
```

설정 파일과 호스트 파일 생성

```jsx
touch ansible.cfg
touch hosts
```

Ansible은 다음 순서로 설정파일을 찾음:

1. `ANSIBLE_CONFIG` \(환경변수\)
2. `ansible.cfg` \(현재디렉터리\)
3. `~/.ansible.cfg` \(홈디렉터리\)
4. `/etc/ansible/ansible.cfg`

프로젝트 별로 설정파일을 두는 것이 좋음.

`ansible.cfg` 파일:

```bash
[defaults]
inventory = hosts
host_key_checking = False
```

* inventory를 hosts 파일로 지정
* Ansible은 기본적으로 호스트 키 확인이 활성화되어 있음\(SSH\). 호스트가 다시 설치되고, known\_hosts 파일에 다른 키가 있는 경우 오류 메시지가 표시됨.

`hosts` 파일:

```bash
[apiservers]
172.25.10.11
172.25.10.21
172.25.10.31
172.25.10.41
172.25.10.51
172.25.10.61

[apiservers:vars]
ansible_connection=ssh
ansible_user=SSH_USER
ansible_ssh_password=SSH_USER_PASSWORD
```

* `ansible_connection` : 노드에 접근하는 방법.
* `ansible_user` : 노드에 접근하는 user.
* `ansible_ssh_password` : 노드에 ssh 접근하기 위한 p/w, 해당 설정을 하지 않고 -k 옵션을 통해 패스워드를 명령줄에서 입력 가능

