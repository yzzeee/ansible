# Ansible 기본

## 인벤토리

관리노드에 접속을 위한 호스트 파일이다.</br>
INI 파일형식과 Yaml 방식이 있는데 일반적으로는 INI 방식을 사용함</br>
YAML 은 띄어쓰기 들여쓰기에 민감하게 반응 하기 떄문에</br>
INI가 작성이 쉽고 관습적으로 더 많이 사용함

섹션 === 그룹
![img.png](../assets/img.png)
그룹에 속한 호스트는 파란 박스
가장 위의 섹션은 그룹이 없는 호스트

그룹안에 그룹이 들어갈 수 있고 중첩 그룹이라고 함

* tip 야물 파일 설정을 하자!
  .vimrc
```shell
# 문서 형식에 문법이 존재한다면 하이라이트를 켜라!
if has("syntax")
  syntax on
endif

set tabstop=2 # set ts=2와 동일, tab을 눌렀을 때 인식하는 칸 수
set expandtab # tab의 캐릭터를 space로 변환
set shiftwidth=2 # set sw=2와 동일, >, <를 사용한 탭 전환을 스페이스로 변환
```

하나의 호스트가 여러개의 그룹에 속할 수 있다.
```shell
all: # 기본으로 all 그룹이 있음
  hosts: # 속성값 (정해진 속성)
    mail.example.com:
    192.168.56.51:
  children: # all 그룹에 속한 하위 그룹들
   webservers: # 그룹이름 원하는 이름으로 지정 가능
     hosts:
       foo.example.com
       bar.example.com
...
```

* 그룹
Ungroup : INI 에서 [] 로 그룹을 지정하지 않았거나</br>
YAML 에서 그룹을 지정하지 않은 호스트들
그외의 Group 들은 모두 all 그룹에 포함됨

`그룹명`을 prod 라는 하위 그룹 (children) 으로 설정
```shell
[prod:children]
그룹명
```

* 호스트 범위 지정
 ```shell
[webservers]
www[01:50].example.com
```
www01 www02 www03 ... www50

* vagrant ssh 접속
```shell
vagrant ssh <vm명>
```
베이그런트 이미지로 만들면 vagrant/vagrant 가 디폴트 사용자로 되어있음
ec2-user(aws), cloud-user(azure) : 각 클라우드 별 디폴트 유저명

* 인벤토리 파일 작성
vi inventory.ini
```shell
[mgmt]
192.168.200.101
192.168.200.102
```

* 인벤토리 파일을 지정해 주지 않으면
`/etc/ansible/hosts` 에 있는 호스트 목록이 출력됨
```shell
$ ansible all --list-hosts 
  hosts (3):
    10.10.10.10
    10.10.10.20
    10.10.10.30
```

* 인벤토리 파일을 지정하여 호스트 목록 출력
```shell
$ ansible all --list-hosts  -i inventory.ini 
  hosts (2):
    192.168.200.101
    192.168.200.102
```

* 그룹내의 호스트 리스트 조회
```shell
ansible <그룹명> --list-hosts
```

* ansible 인벤토리의 목록 조회
```shell
ansible-inventory -i inventory.init --list
```
```shell
$ ansile-inventory --list
{
    "_meta": {
        "hostvars": {}
    },
    "all": {
        "children": [
            "ungrouped"
        ]
    },
    "ungrouped": {
        "hosts": [
            "10.10.10.10",
            "10.10.10.20",
            "10.10.10.30"
        ]
    }
}

```

* 그룹에 따라 그래프 형태로 보여주기
```shell
ansible-inventory --graph
```

* 호스트나 그룹의 변수 목록 확인
```shell
ansible-inventory -i <인벤토리명> --host 호스트명
```
```shell
$ ansible-inventory -i inventory.ini --host 192.168.200.101
{}
```
현재는 아무런 변수가 없어서 나오는 것이 없음

* 정규화 표현식 이용
```shell
ansible '192.168.200.*' --list-hosts -i inventory.ini
```

* 패턴
![inventory pattern](../assets/inventory_pattern.png)
그룹과 그룹을 짬뽕하지 마세요! 의도 하지 않은 일이 일어 날 수 있음!
필요하면 그룹을 따로 만드세요!

### 구성 파일

ansible의 작동방식을 구성하는 파일이다.
* Ansible 구성 파일 우선 순위
1. ANSIBLE_CONFIG 환경 변수
2. 현재 디렉토리의 ansible.cfg
3. 홈디렉토리의 ~/.ansible.cfg
4. /etc/ansible/ansible.cfg

* 현재 설정파일 위치 확인
```shell
$ ansible --version
ansible [core 2.11.5] 
  config file = /home/jollaman999/vagrant/ansible/ansible.cfg
```
```shell
$ touch /tmp/ansible.cfg
$ export ANSIBLE_CONFIG=/tmp/ansible.cfg
```
* 구성 파일 설정
```text
  [default] 섹션
- inventory : 인벤토리 파일 위치 지정
- remote_user : SSH 접속할 사용자 이름
- ask_pass : SSH 비밀번호 물어 볼지 설정 (기본 false / 기본으로 key 인증 방식 사용)
  [privilege_escalation] # 권한 상승 (su는 보안상 안좋아 ! sudo를 사용!)
become = true # false가 기본값!
become_method = sudo
become_user = root
become_ask_pass = false # 기본이 false이다! 패스워드 안물어본다.
패스워드 물어보는거는 자동화의 이점을 살리지 못하니깐 .. 쫌.. 
```


[privilege_escalation]
- become : 권한 상승 여부 (기본 : false)
- become_method : 권한 상승 방법 (기본 : sudo)
- become_user : 권한 상승할 사용자 (기본 : root)
- become_ask_pass : 권한 상승 방법의 패스워드 요청/입력 여부 (기본 : false)

엔시블은 기본적으로 root 계정을 사용하는데 기본계정을 일반 사용자로 접속 했다면</br>
관리자 권한 필요한 작업을 해야하는데
이

관리하는 계정의 계정정보는 보통 동일하게 사용하면 좋을 걸~~ 가능하면!


베이그런트로 생성한 vm은 기본적으로 passwd less sudo가 설정되어 /etc/sudoers.d/vagrant 여기 등록됨


* `~/.ansible.cfg` 작성
```shell
[defaults]
remote_user = varant
inventory = ~/inventory.ini
ask_pass = false

[privilege_Escalation]
become = false
become_method = sudo
become_user = root
become_ask_pass = false
```

* `~/inventory.ini` 작성
```shell
[mgmt]
192.168.200.101
192.168.200.102
```
* 현재가장 우선 순위가 높은 config 파일 보여줌
```shell
ansible-config view
```

* ansible에서 설정가능한 모든 설정들을 보여줌
```shell
ansible-config list
```

* 현재 적용된 모든 구성정보 확인
```shell
ansible-config dump
```
초록이 : 변경 없는 기본값

## 관리노드 연결
ansible의 제어노드는 기본적으로 openssh로 관리노드에 접근함
ControlPersist (성능 향상 기능)
ansible 사용 시 paramiko 어쩌고 할 때가 있는데 ControlPersist 관련된 거 ..

```shell
ssh-keygen -t rsa -f ~/.ssh/id_rsa -N ''
ssh-copy-id vagrant@192.168.200.101
ssh-copy-id vagrant@192.168.200.102
```
