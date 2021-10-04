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