# 13. AWS CLI 및 Terraform 설치

## 13.1 AWS CLIv2 설치
```shell
curl https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip -o awscliv2.zip
unzip awscliv2.zip 
sudo ./aws/install 
aws --version
```

## 13.2 Terraform 설치
```shell
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
```
```shell
sudo apt-get update && sudo apt-get install terraform
```

* 테라폼 자동완성 설치
```shell
terraform -install-autocomplete
```

테라폼에는 -- 옵션이 없음 모두 - 임.

* aws 설정
```shell
$ aws configure
AWS Access Key ID [None]: AKIA3FD5X6HEKCRCAXUH
AWS Secret Access Key [None]: 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
Default region name [None]: ap-northeast-2
Default output format [None]:

$ aws sts get-caller-identity
{
    "UserId": "AIDAYBLKMZRHX7Y2J546X",
    "Account": "552663305295",
    "Arn": "arn:aws:iam::552663305295:user/iac-admin"
}
```