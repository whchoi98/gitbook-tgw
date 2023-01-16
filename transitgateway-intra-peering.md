---
description: 'Update: 2022-06-05'
---

# TransitGateway Intra-Peering

## 1.TGW Intra-Peering 소개

TransitGateway는 서로 다른 리전 또는 동일 리전에서 TransitGateway를 Peering 할 수 있습니다.&#x20;

리전 내 피어링 기능을 사용하면 더 이상 동일한 AWS 리전의 다른 Transit Gateway 간에 트래픽을 라우팅하기 위해 여러 Transit Gateway 간에 브리지 VPC를 생성하거나 단일 VPC를 여러 Transit Gateway에 연결할 필요가 없습니다. 리전 내 피어링은 별도의 Transit Gateway를 통해 서비스 및 관리되는 온프레미스 네트워크와 VPC 간의 라우팅 및 상호 연결을 간소화합니다. 이 기능을 통해 고객은 별도의 관리 도메인이 있는 여러 Transit Gateway를 유연하게 배포할 수 있으며 이러한 Transit Gateway를 보다 기본적으로 상호 연결하는 손쉬운 방법을 제공합니다. 리전 내 피어링을 사용하면 유연한 네트워크 토폴로지를 구축하고 네트워크를 동일한 AWS 리전의 타사 또는 파트너 관리형 네트워크와 쉽게 통합할 수 있습니다.

아래 구성도는 동일 리전에서 여러개의 TransitGateway를 수용하는 디자인입니다.&#x20;

![](<.gitbook/assets/image (146) (1).png>)

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

![](<.gitbook/assets/image (138) (1) (1).png>)

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
  --template-file "/home/ec2-user/environment/tgw/Seoul-TGW-PART2.yml" \
  --capabilities CAPABILITY_NAMED_IAM
  
```

정상적으로 구성되면 아래와 같이 Cloudformation에서 확인 할 수 있습니다.  Transitgateway는 각 5분 내외에 생성됩니다.

![](<.gitbook/assets/image (143) (1) (1) (1) (1).png>)

### Task4. VPC, EC2 구성 확인하기

Cloudformation 으로 생성된 자원들이 정상적으로 배포되었는지 확인합니다

**`AWS 관리콘솔 - VPC`** 를 선택합니다.

VPC가 정상적으로 생성되었는지 확인합니다.

![](<.gitbook/assets/image (145) (1).png>)

AWS 관리콘솔 - EC2를 선택합니다.

EC2가 정상적으로 생성되었는지 확인합니다.

![](<.gitbook/assets/image (139) (1) (1).png>)

### Task5. TGW 구성 확인

**`AWS 관리콘솔 - VPC - TransitGateway`** 를 선택해서, Transit Gateway 정상적으로 구성되었는지 확인합니다.

![](<.gitbook/assets/image (132) (1) (1).png>)

![](<.gitbook/assets/image (136) (1) (1).png>)

### Task6. TGW Attachment 확인.

**`VPC-Transit Gateway-Transit Gateway 연결` 을 선택해서, Transit Gateway attachment가 정상적으로 구성되었는지 확인합니다.**

![](<.gitbook/assets/image (134) (2).png>)

3개의 Attachment를 선택하면, 이미 "Seoul-PART-PRD, STG, DEV VPC"의 TGW-Subnet ID에 연결되어 있는 것을 확인할 수 있습니다. 또한 Routing Table에 Association 된 상태도 확인이 가능합니다.&#x20;

* Seoul-TGW-PART2-Attach-Seoul-VPC-PART-PRD - Seoul-PART-PRD VPC 에 연결
* Seoul-TGW-PART2-Attach-Seoul-VPC-PART-DEV - Seoul-PART-STG VPC 에 연결
* Seoul-TGW-PART2-Attach-Seoul-VPC-PART-DEV - Seoul-PART-DEV VPC 에 연결

1. **TGW Routing Table과 Attachment가 연결된 상태를 확인**
2. **Attachment가 VPC의 어떤 Subnet과 연결되었는지 확인**

### Task7. TGW Routing Table 확인.&#x20;

**`VPC-Transit Gateway-Transit Gateway- Transit Gateway 라우팅 테이블`** 을 선택해서 라우팅 테이블 구성을 확인해 봅니다. Associations(연결) 와 Propagation(전파), Routes(경로 탭을 눌러서, Seoul-VPC-PART-PRD/STG/DEV 연결과 각 VPC의 CIDR가 정상적으로 업데이트 되었는지 확인합니다.&#x20;

![](<.gitbook/assets/image (149) (1).png>)

![](<.gitbook/assets/image (148) (1).png>)

![](<.gitbook/assets/image (137) (1).png>)

이제 Cloudformation을 통해서 TransitGateway Intra-Peering이 모두 정상적으로 구성되었습니다.&#x20;

## 3. TGW Peering 구성

### Task8. SSM 에서 인스턴스 확인

모든 랩의 구성 시험은 Private 인스턴스로 시험합니다. Cloudformation을 통해 System Manager와 Session Manager를 사용할 수 있도록 자동 배포 구성하였습니다.

Session Manager를 사용할 수 있도록 아래 같이 각 PC환경에 맞추어서 AWS Session Manager Plugin을 설치합니다. Cloud9을 사용하거나 웹콘솔에서 Session Manager를 사용하면 각 PC환경에서 설치할 필요가 없습니다.

Cloud9 터미널에서 아래 aws cli 명령을 실행하여 생성된 Seoul-의 EC2 인스턴스를 확인합니다.

아래와 같이 Cloud9에서 shell을 실행해 봅니다.

```
~/environment/tgw/aws_ec2_ext.sh |grep "Seoul-VPC-PART-PRD"
~/environment/tgw/aws_ec2_ext.sh |grep "Seoul-VPC-PART-STG"
~/environment/tgw/aws_ec2_ext.sh |grep "Seoul-VPC-PART-DEV"

```

아래와 같은 결과를 확인할 수 있습니다.&#x20;

```
whchoi:~/environment $ ~/environment/tgw/aws_ec2_ext.sh |grep "Seoul-VPC-PART-PRD"
|  Seoul-VPC-PART-PRD-Private-10.11.21.101              |  ap-northeast-2a |  i-0ccb1eb5b7316fa2b |  t3.micro |  ami-0195322846474ddb9 |  running |  10.11.21.101 |  None            |
|  Seoul-VPC-PART-PRD-Public-10.11.11.101               |  ap-northeast-2a |  i-070055b1b9272c872 |  t3.micro |  ami-0195322846474ddb9 |  running |  10.11.11.101 |  3.35.217.240    |
whchoi:~/environment $ ~/environment/tgw/aws_ec2_ext.sh |grep "Seoul-VPC-PART-STG"
|  Seoul-VPC-PART-STG-Public-10.12.11.101               |  ap-northeast-2a |  i-0cfe93dd4ea738fd1 |  t3.micro |  ami-0195322846474ddb9 |  running |  10.12.11.101 |  3.38.255.132    |
|  Seoul-VPC-PART-STG-Private-10.12.21.101              |  ap-northeast-2a |  i-0b18055e35d518fbc |  t3.micro |  ami-0195322846474ddb9 |  running |  10.12.21.101 |  None            |
whchoi:~/environment $ ~/environment/tgw/aws_ec2_ext.sh |grep "Seoul-VPC-PART-DEV"
|  Seoul-VPC-PART-DEV-Public-10.13.11.101               |  ap-northeast-2a |  i-0ddf9cd0c997624a3 |  t3.micro |  ami-0195322846474ddb9 |  running |  10.13.11.101 |  13.124.93.54    |
|  Seoul-VPC-PART-DEV-Private-10.13.21.101              |  ap-northeast-2a |  i-0fd08fd203278c5ce |  t3.micro |  ami-0195322846474ddb9 |  running |  10.13.21.101 |  None            |
```

ssm plugin을 통해서 인스턴스 ID 기반으로, 직접 Private Instance에 접속합니다.아래와 같은 명령을 통해서 직접 Private Instance에 접속합니다.&#x20;

```
aws ssm start-session --target "Seoul-VPC-PART-PRD-Private-10.11.21.101 id"
```

SSM을 통해서 Cloud9에서 터미널 창을 추가로 오픈하고, 아래와 같이 bash 콘솔로 접속하고, 시험할 호스트들을 host file에 등록합니다.&#x20;

```
sudo -s
echo 10.11.11.101 Seoul-VPC-PART-PRD-Public >> /etc/hosts 
echo 10.11.21.101 Seoul-VPC-PART-PRD-Private >> /etc/hosts 
echo 10.12.11.101 Seoul-VPC-PART-STG-Public >> /etc/hosts 
echo 10.12.21.101 Seoul-VPC-PART-STG-Private >> /etc/hosts 
echo 10.13.11.101 Seoul-VPC-PART-DEV-Public >> /etc/hosts 
echo 10.13.21.101 Seoul-VPC-PART-DEV-Private >> /etc/hosts 
```

### Task9. 시나리오 이해하기

**1.서밋 컴퍼니는 아래와 같은 VPC를  서울 리전에 소유하고 있습니다.**

* Production Workload : Seoul-VPC-PART-PRD
* Staging Workload : Seoul-VPC-PART-STG
* Dev Workload : Seoul-VPC-PART-Dev

**2. Production VPC는 이미 TGW를 생성해서 사용하고 있습니다 Staging,Dev VPC도 이미 TGW를 생성해서 사용하고 있습니다**

**3. 동일 리전에서 TGW를 연결하고 Staging,DEV VPC의 워크로드의 Private Subnet의 워크로드들은 Production VPC를 통해서 외부와 연결하고 서로 통신을 하기를 원합니다**

목표 구성과 필요작업은 아래와 같습니다.

![](<.gitbook/assets/image (137) (2).png>)

### **Task10. Seoul 리전에서 TGW간 연결 (Peering)**

Seoul-TGW-PART1 , Seoul-TGW-PART2를 동일 리전안에서 연결해 봅니다.&#x20;

Seoul-TGW-PART1 , Seoul-TGW-PART2에는 이미 각 VPC의 CIDR가 Propagated(전파 가 구성되어 있기 때문에, TGW에서 작업은 불필요합니다.&#x20;

Seoul-TGW-PART2 TGW ID를 복사해 둡니다. Peering을 위해서는 TGW ID를 알고 있어야 합니다.&#x20;

![](<.gitbook/assets/image (138) (1).png>)

**`AWS 관리콘솔 - VPC - Transit Gateway - Transit Gateway`** 연결 을 선택합니다.

**`Create Transit Gateway Attachment(Transit Gateway 연결 생성)`** 를 선택합니다.

1. Peering 이름을 생성합니다.  **`Seoul-TGW-PART1-To-PART2`**
2. Trasit gateway ID를 선택합니다. **`Seoul-TGW-PART1`** &#x20;
3. 연결유형을 Peering Connection으로 선택합니다. &#x20;
4. 계정 - 내 계정을 선택
5. 리전 - ap-northeast-2 를 선택&#x20;
6. Transit gateway 수락자 - 앞서 복사해 둔 Seoul-TGW-PART2 Transit gateway ID를 입력합니다.

![](<.gitbook/assets/image (144) (1) (1).png>)

작업이 완료되면 아래와 같이 Seoul-TGW-PART2가 수락할때 까지 Pending 상태가 됩니다.

![](<.gitbook/assets/image (142) (1).png>)

Seoul-TGW-PART2에서 수락하지 않으면 연결되지 않습니다.

비어있는 Name을 **`Seoul-TGW-PART2-To-PART1`** 으로 변경하고 ,상단메뉴 **`"작업"`** 을 선택하고 **`Transit Gateway 연결수락`**을 선택합니다.&#x20;

![](<.gitbook/assets/image (148) (2).png>)

Accept(수락를 선택하면, pending 으로 전환되고 3\~4분 이후 available로 변경됩니다.&#x20;

![](<.gitbook/assets/image (143) (1) (1).png>)

이제 Attachment가 Available(가용)으로 변경되면,**`Seoul-TGW-PART1-RT`** 라우팅 테이블의 **`Transit Gateway-Transit Gateway Route Table`** 탭에서 생성한 Peering Attachment를 Association(연결)을 시켜 줍니다.&#x20;

![](<.gitbook/assets/image (136) (1) (2).png>)

![](<.gitbook/assets/image (149).png>)

![](<.gitbook/assets/image (135) (1).png>)

연결 생성 이후 Trasit Gateway 라우팅테이블의 연결 탭에서 3\~4분 이후 Associated (연결) 됩니다.&#x20;

![](<.gitbook/assets/image (144) (1).png>)

**`Seoul-TGW-PART2-RT`** 라우팅 테이블도 **`Transit Gateway-Transit Gateway Route Table`** 탭에서 생성한 Peering Attachment를  Association(연결)을 시켜 줍니다.&#x20;

![](<.gitbook/assets/image (139) (1).png>)

![](<.gitbook/assets/image (140) (1).png>)

연결 생성 이후 Trasit Gateway 라우팅테이블의 연결 탭에서 3\~4분 이후 Associated (연결) 됩니다.&#x20;

![](<.gitbook/assets/image (129) (1).png>)

### Task11. Transit Gateway 라우팅 테이블 변경

Peering은 구성을 완료했지만, 상호간의 라우팅 구성이 되어 있지 않았습니다.

먼저 Seoul-TGW-PART1에서 Seoul-TGW-PART2로 라우팅을 구성해 줍니다.

**`Transit Gateway - Transit Gateway 라우팅 테이블`** 을 선택합니다.

**`Seoul-TGW-PART1-RT`** 를 선택하고, Route(경로) 탭을 선택하고, Create static Route(정적경로생성)를 선택합니다.

![](<.gitbook/assets/image (132) (1) (2).png>)

Static Route(정적 경로) 설정을 위한 CIDR, Attachment를 선택합니다

* CIDR : 10.0.0.0/8 (STG, DEV VPC CIDR 대역 )&#x20;
* Attachment : Seoul-TGW-PART1-To-PART2 선택

![](<.gitbook/assets/image (143) (1).png>)

Static Route(정적 경로) 설정을 위한 CIDR, Attachment를 선택합니다

* CIDR : 0.0.0.0/0 (STG, DEV VPC가 인터넷 트래픽으로 가기 위한 CIDR )&#x20;
* Attachment : Seoul-TGW-PART1-Attach-Seoul-VPC-PART-PRD 선택

![](<.gitbook/assets/image (135) (2).png>)

연결 생성 이후 Trasit Gateway 라우팅테이블의 연결 탭에서 경로가 생성됩니다.&#x20;

![](<.gitbook/assets/image (145).png>)

Seoul-TGW-PART2에서도 Seoul-TGW-PART1 으로 라우팅을 구성해 줍니다.

**`Transit Gateway - Transit Gateway 라우팅 테이블`** 을 선택합니다.

**`Seoul-TGW-PART2-RT`** 를 선택하고, Route(경로) 탭을 선택하고, Create static Route(정적경로생성)를 선택합니다.

![](<.gitbook/assets/image (141).png>)

Static Route(정적 경로) 설정을 위한 CIDR, Attachment를 선택합니다

* CIDR : 0.0.0.0/0 (STG, DEV VPC에서 인터넷 경로, PRD 경로를 위한 CIDR)
* Attachment : Seoul-TGW-PART2-To-PART1 선택

![](<.gitbook/assets/image (138).png>)

연결 생성 이후 Trasit Gateway 라우팅테이블의 연결 탭에서 경로가 생성됩니다.

![](<.gitbook/assets/image (142).png>)

## 4. VPC Routing 구성

앞서 TGW Routing을 모두 구성하더라도, VPC에서 라우팅 구성이 되어 있지 않기 때문에 정상적으로 트래픽이 처리되지 않습니다. Seoul-VPC-PART-PRD 에서 Seoul-VPC-PART-STG,DEV를 위한 라우팅 테이블과 Seoul-VPC-PART-STG,DEV에서 Seoul-VPC-PRD, Internet 연결을 위한 라우팅 테이블을 구성해야 합니다

### Task 12. Seoul-VPC-PART-PRD 라우팅테이블 구성

Seoul-VPC-PART-PRD VPC에서 다음과 같은 라우팅 테이블을 추가해 줍니다. &#x20;

* Seoul-VPC-PART-PRD-PublicRT - 10.0.0.0/8 경로가 TGW로 향하도록 구성
* Seoul-VPC-PART-PRD-Private-Subnet-A-RT - 10.0.0.0/8 경로가 TGW로 향하도록 구성&#x20;

**`VPC - 라우팅 테이블`** 에서 **`Seoul-VPC-PART-PRD-PublicRT`** 을 선택합니다.&#x20;

라우팅 탭에서 라우팅 편집을 선택합니다.&#x20;

![](<.gitbook/assets/image (143).png>)

10.0.0.0/8 목적지의 대상을 TGW로 선택하여 추가하고, 저장합니다. &#x20;

![](<.gitbook/assets/image (146).png>)

저장 이후 정상적으로 라우팅이 추가되었는지 확인합니다. &#x20;

![](<.gitbook/assets/image (136) (1).png>)

**`Seoul-VPC-PART-PRD-Private-Subnet-A-RT`** 라우팅도 동일하게 추가해 줍니다.&#x20;

![](<.gitbook/assets/image (139).png>)



### Task 13. Seoul-VPC-PART-STG,DEV 라우팅테이블 구성

Seoul-VPC-PART-STG,DEV VPC에서 다음과 같은 라우팅 테이블을 추가해 줍니다. &#x20;

* Seoul-VPC-PART-STG-Private-Subnet-A-RT - 0.0.0.0/0 경로가 TGW로 향하도록 구성
* Seoul-VPC-PART-DEV-Private-Subnet-A-RT - 0.0.0.0/0 경로가 TGW로 향하도록 구성

**`VPC - 라우팅 테이블`** 에서 **`Seoul-VPC-PART-STG-Private-Subnet-A-RT`** 을 선택합니다.&#x20;

라우팅 탭에서 라우팅 편집을 선택합니다.&#x20;

![](<.gitbook/assets/image (132) (1).png>)

0.0.0.0/0 목적지의 대상이 NAT Gateway로 되어 있습니다. 제거하고 0.0.0.0/0 목적지로 Transit Gateway로 신규 추가합니다. &#x20;

![](<.gitbook/assets/image (144).png>)

저장 이후 정상적으로 라우팅이 추가되었는지 확인합니다.  &#x20;

![](<.gitbook/assets/image (147).png>)

**`Seoul-VPC-PART-DEV-Private-Subnet-A-RT`** 라우팅도 동일하게 추가해 줍니다.&#x20;

![](<.gitbook/assets/image (140).png>)

## 5.확인&#x20;

### Task14. PRD,STG,DEV VPC EC2에서 확인. &#x20;

Cloud9에서 PRD, STG, DEV VPC의 EC2로 접속해 봅니다. 연결은 SSM(Session Manager)를 통해서 접속합니다.

```
aws ssm start-session --target "Seoul-VPC-PART-PRD-Private-10.11.21.101 id"
aws ssm start-session --target "Seoul-VPC-PART-STG-Private-10.12.21.101 id"
aws ssm start-session --target "Seoul-VPC-PART-DEV-Private-10.13.21.101 id"
```

아래 처럼 정상적으로 통신이 되는지 확인합니다.&#x20;

* Seoul-VPC-PART-PRD-Private-10.11.21.101
  * [ ] Seoul-VPC-PART-STG-Private-10.12.21.101 로 통신 가능 여부 확인&#x20;
  * [ ] Seoul-VPC-PART-DEV-Private-10.13.21.101 로 통신 가능 여부 확인&#x20;
* Seoul-VPC-PART-STG-Private-10.12.21.101
  * [ ] Seoul-VPC-PART-PRD-Private-10.11.21.101 로 통신 가능 여부 확인&#x20;
  * [ ] www.aws.com 로 통신 가능 여부 확인
* Seoul-VPC-PART-DEV-Private-10.13.21.101
  * [ ] Seoul-VPC-PART-PRD-Private-10.11.21.101 로 통신 가능 여부 확인&#x20;
  * [ ] www.aws.com 로 통신 가능 여부 확인

