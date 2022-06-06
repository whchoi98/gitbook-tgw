---
description: 'Update: 2022-06-05'
---

# TransitGateway Intra-Peering

## 1.TGW Intra-Peering 소개

TransitGateway는 서로 다른 리전 또는 동일 리전에서 TransitGateway를 Peering 할 수 있습니다.&#x20;

리전 내 피어링 기능을 사용하면 더 이상 동일한 AWS 리전의 다른 Transit Gateway 간에 트래픽을 라우팅하기 위해 여러 Transit Gateway 간에 브리지 VPC를 생성하거나 단일 VPC를 여러 Transit Gateway에 연결할 필요가 없습니다. 리전 내 피어링은 별도의 Transit Gateway를 통해 서비스 및 관리되는 온프레미스 네트워크와 VPC 간의 라우팅 및 상호 연결을 간소화합니다. 이 기능을 통해 고객은 별도의 관리 도메인이 있는 여러 Transit Gateway를 유연하게 배포할 수 있으며 이러한 Transit Gateway를 보다 기본적으로 상호 연결하는 손쉬운 방법을 제공합니다. 리전 내 피어링을 사용하면 유연한 네트워크 토폴로지를 구축하고 네트워크를 동일한 AWS 리전의 타사 또는 파트너 관리형 네트워크와 쉽게 통합할 수 있습니다.

아래 구성도는 동일 리전에서 여러개의 TransitGateway를 수용하는 디자인입니다.&#x20;

![](<.gitbook/assets/image (146).png>)

## 2.환경 구성하기

앞서 **TransitGateway 멀티 어카운트** Chapter를 수행하였다면  **`Task1. Cloud9 사전 준비하기`** 는 생략해도 됩니다.&#x20;

Lab 구성을 위해서, 새로운 Account에서 수행합니다.&#x20;

### Task 1. Cloud9 사전 준비

**`새로운 계정에 접속`** 하고, Cloudformation을 통해 기본이 되는 VPC구성을 먼저 구성합니다.

Task 들을 수행하기 위해서, 새로운 계정에도 Cloud9을 구성하는 것이 좋습니다. Cloud9에는 아래와 같이 동일하게 aws cli, ssm plugin 등을 설치하고, ssh key를 생성합니다.&#x20;

```
# git clone
cd ~/environment
git clone https://github.com/whchoi98/tgw.git

# AWS CLI upgrade
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
source ~/.bashrc

# aws cli 자동완성 설치 
which aws_completer
export PATH=/usr/local/bin:$PATH
source ~/.bash_profile
complete -C '/usr/local/bin/aws_completer' aws

##ssm plugin install
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/linux_64bit/session-manager-plugin.rpm" -o "session-manager-plugin.rpm"
sudo yum install -y session-manager-plugin.rpm

##ssh key를 생성합니다.
##ssh key name은 mykey 로 구성합니다
ssh key-gen

mv mykey ./mykey.pem
chmod 400 ./mykey.pem

## Public Key를 ap-northeast-2로 전송합니다
aws ec2 import-key-pair --key-name "mykey" --public-key-material fileb://mykey.pub --region ap-northeast-2

```



### Task 2. Cloudformation 생성 - VPC

Cloud9 terminal 에서 aws cli 명령어로 Cloudformation 코드를 실행해서 , 서울리전의 PRD, STG, DEV VPC를 생성합니다.&#x20;

* Seoul-VPC-PART-PRD 생성

```
# Seoul VPC PATR Production 생성
aws cloudformation deploy \
  --stack-name "Seoul-VPC-PART-PRD" \
  --template-file "/home/ec2-user/environment/tgw/Seoul-VPC-PART-PRD.yml" \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides "KeyPair=mykey"
  
```

* Seoul-VPC-PART-STG 생성

```
# Seoul VPC PATR Staging 생성
aws cloudformation deploy \
  --stack-name "Seoul-VPC-PART-STG" \
  --template-file "/home/ec2-user/environment/tgw/Seoul-VPC-PART-STG.yml" \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides "KeyPair=mykey"
  
```

* Seoul-VPC-PART-DEV 생성

```
# Seoul VPC PATR Develop 생성
aws cloudformation deploy \
  --stack-name "Seoul-VPC-PART-DEV" \
  --template-file "/home/ec2-user/environment/tgw/Seoul-VPC-PART-DEV.yml" \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides "KeyPair=mykey"
  
```

정상적으로 구성되면 아래와 같이 AWS 서비스 Cloudformation 콘솔창에서 확인 할 수 있습니다. VPC는 각 3분 내외에 생성됩니다.

![](<.gitbook/assets/image (138).png>)

### Task3. Cloudformation 생성 - TransitGateway

Cloud9 terminal 에서 aws cli 명령어의 Cloudformation 코드를 실행해서 , PART1, PART2 TrasitGateway들을 생성합니다.&#x20;

* Seoul-TGW-PART1 생성 - PRD VPC와 연결되는 TGW 입니다.

```
# Seoul VPC PATR1 TGW 생성
aws cloudformation deploy \
  --stack-name "Seoul-TGW-PART1" \
  --template-file "/home/ec2-user/environment/tgw/Seoul-TGW-PART1.yml" \
  --capabilities CAPABILITY_NAMED_IAM
  
```

* Seoul-TGW-PART2 생성 - STG , DEV VPC와 연결되는 TGW 입니다.&#x20;

```
# Seoul VPC PATR2 TGW 생성
aws cloudformation deploy \
  --stack-name "Seoul-TGW-PART2" \
  --template-file "/home/ec2-user/environment/tgw/Seoul-TGW-PART1.yml" \
  --capabilities CAPABILITY_NAMED_IAM
  
```

정상적으로 구성되면 아래와 같이 Cloudformation에서 확인 할 수 있습니다.  Transitgateway는 각 5분 내외에 생성됩니다.

![](<.gitbook/assets/image (143).png>)

### Task4. VPC, EC2 구성 확인하기

Cloudformation 으로 생성된 자원들이 정상적으로 배포되었는지 확인합니다

**`AWS 관리콘솔 - VPC`** 를 선택합니다.

VPC가 정상적으로 생성되었는지 확인합니다.

![](<.gitbook/assets/image (145).png>)

AWS 관리콘솔 - EC2를 선택합니다.

EC2가 정상적으로 생성되었는지 확인합니다.

![](<.gitbook/assets/image (139).png>)

### Task5. TGW 구성 확인

**`AWS 관리콘솔 - VPC - TransitGateway`** 를 선택해서, Transit Gateway 정상적으로 구성되었는지 확인합니다.

![](<.gitbook/assets/image (136).png>)

#### Task6. TGW Attachment 확인.

**`VPC-Transit Gateway-Transit Gateway 연결` 을 선택해서, Transit Gateway attachment가 정상적으로 구성되었는지 확인합니다.**

****

Seoul-TGW-Attach-IAD-VPC를 선택하면, 이미 "IAD-VPC"의 TGW-Subnet ID에 연결되어 있는 것을 확인할 수 있습니다. 또한 Routing Table에 Association 된 상태도 확인이 가능합니다.

1. **TGW Routing Table과 Attachment가 연결된 상태를 확인**
2. **Attachment가 VPC의 어떤 Subnet과 연결되었는지 확인**
