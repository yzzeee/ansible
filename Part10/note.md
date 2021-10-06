# 10. 암호화

## 10.1

#### 1) 단일 볼트 패스워드

## 10.2 파일 수준 암호화 관리

#### 1) 암호화 된 파일 생성
* 생성
```shell
ansible-vault create encrypt.yaml
New Vault password: 
Confirm New Vault password: 
$ ls
encrypt.yaml
```

* 파일 내용 쓰고서 보면 aes256 으로 암호화된 것을 볼수 있음
```shell
$ cat encrypt.yaml 
$ANSIBLE_VAULT;1.1;AES256
36653234326666363436646233343936366536633064393535386438646430643863383435636166
6264663336366162336436653934303864626363323264320a386364363630656265373333643963
34353139633337346432343639616364613066653665373466653763643438613730323361376639
6338396632636635370a393861646265366131643437653862336438323763323831353663646332
32353864303364633634343163656534623163316435353663653266663537316431
```

* 수정 하기
```shell
$ ansible-vault edit encrypt.yaml
Vault password:
```

* 암호 풀기
```shell
$ ansible-vault decrypt encrypt.yaml
Vault password: 
Decryption successful
$ ls
encrypt.yaml
$ cat encrypt.yaml 
- hosts: 192.168.200.101
```

* 암호화된 yaml 파일 실행
```shell
$ ansible-playbook encrypt.yaml --ask-vault-pass
Vault password: 

PLAY [192.168.200.101] ***************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [192.168.200.101]

PLAY RECAP ***************************************************************************************
192.168.200.101            : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

* 패스워드 변경
```shell
$ ansible-vault rekey encrypt.yaml 
Vault password: 
New Vault password: 
Confirm New Vault password: 
Rekey successful
```

* 패스워드가 저장되어 있는 파일 지정하기
```shell
echo "pass123" > vpass
ansible-playbook encrypt.yaml --vault-password-file vpass
```

보안상 좋은 방법 아님

* ansible.cfg 에다가 참조할 비밀번호 적어놓으면.. 인크립트 안해도 쓸 수 있음
```ini
[defaults]
vault_password_file = vpass
inventory = inventory
```
* inventory
```text
192.168.200.1
```
* encrypt 파일 생성 (비밀번호: pass123)
```shell
ansible-vault create encrypt.yaml
```
* vpass
```text
pass123
```
* Run 
```shell
ansible-playbook encrypt.yaml
```

## 10.3 가변 수준 암호화

파일 전체가 아닌 일부만 암호화 할 수 있다.
```shell
ansible-vault encrypt_string --vault-password-file vault_pass 'foobar' --name the_secret 'abc'
```

* vi test.yaml
```yaml
- hosts: 192.168.200.101
  var_files: vars.yaml
  vars:
    the_secret: !valt | 
      193808108394718381092489018908091839082904819080184098309819481038378957283794
      325029375825823847284729882374827857294829859289482094820482385925728578782758
      847925788572384234792829823492492872834028948058294892384275874190810489014894
  tasks:
    - debug:
        msg: "{{ the_secret }}"
```

