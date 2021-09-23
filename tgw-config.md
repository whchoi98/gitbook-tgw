# TransitGateway 구성

## 1.TransitGateway 기본 구성

### 구성 아키텍쳐 소개

AWS TransitGateway의 기본 동작 이해를 위해, 가장 기본이 되는 디자인을 먼저 구성해 봅니다.

아래 그림은 이번 Chapter에서 구성해 볼 아키텍쳐 입니다.

![](.gitbook/assets/image%20%281%29.png)

### Task1. VPC 구성하기

Cloudformation을 통해 기본이 되는 VPC구성을 먼저 구성합니다.

**1.사전 준비하기**

서울 리전에 4개의 VPC를 구성하고, 사전에 구성된 TGW를 배포합니다. 이미 사전 준비 단계에서 다운로드 받아 두었습니다.

**2.Cloudformation 생성.**

Seoul-VPC-HQ, Seoul-VPC-PRD, Seoul-VPC-STG, Seoul-VPC-DEV를 Cloudformation 을 기반으로 생성합니다.

**AWS 콘솔에서 서울 리전 \(ap-northeast-2\)를 선택하고, Cloudformation 서비스를 선택합니다.**

**Cloudformation에서 먼저 새로운 스택을 생성합니다.**

\*\*\*\*

**앞서 다운로드 받은 yaml 파일들 중에 `Seoul-VPC-HQ.yml` 파일을 업로드 합니다.**

```text
Seoul-VPC-HQ
```

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2814%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2814%29.png)

다음을 선택하고, 아래와 같아 스택이름은 파일명과 동일하게 입력합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2825%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2825%29.png)

{% hint style="warning" %} 스택이름을 파일명과 다르게 입력하지 마십시요. 이후 과정에서 TransitGateway의 yaml파일은 , VPC yml 에서 생성된 값들을 import 해서 TGW를 생성합니다. 스택이름을 파일명과 다르게 할 경우, TGW를 생성할 때 에러가 발생합니다. {% endhint %}

별도로 설정 변경없이, 다음 단계를 진행하고 , 승인을 선택하고 스택생성합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2838%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2838%29.png)

**다운로드 받은 yaml 파일 3개를 추가로 반복적으로 수행합니다.**

```text
Seoul-VPC-PRD
Seoul-VPC-STG
Seoul-VPC-DEV
```

4개의 VPC가 모두 정상적으로 구성되면 아래와 같이 Cloudformation에서 확인 할 수 있습니다. 4개의 VPC는 각 3분 내외에 생성됩니다. 동시에 수행해도 가능합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%283%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%283%29.png)

#### 

#### 

#### Task2. TGW구성하기.

4개의 VPC를 연결할 TransitGateway를 Region에 Cloudformation으로 생성합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%282%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%282%29.png)

다음을 선택하고, 아래와 같아 스택이름은 파일명과 동일하게 입력합니다. \(TGW는 스택이름을 다르게 지정해도, 본 랩을 구성하는데 문제가 없습니다.\)

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2821%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2821%29.png)

5분 이내에 TransitGateway가 완성됩니다.�

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%286%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%286%29.png)

### 

### 2.TransitGateway 구성 확인

#### 

#### Task3.VPC, EC2 구성 확인

AWS 관리콘솔 - VPC 를 선택합니다.

4개의 VPC가 정상적으로 생성되었는지 확인합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2850%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2850%29.png)

AWS 관리콘솔 - EC2를 선택합니다.

EC2가 정상적으로 생성되었는지 확인합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2876%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2876%29.png)

#### 

#### Task 4. TGW 구성 확인

VPC - TransitGateway를 선택해서, Transit Gateway 정상적으로 구성되었는지 확인합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%285%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%285%29.png)

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2827%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2827%29.png)

#### 

#### 

#### Task5. TGW Attachment 확인.

**VPC-Transit Gateway-Transit Gateway 연결 을 선택해서, Transit Gateway attachment가 정상적으로 구성되었는지 확인합니다.**

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2832%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2832%29.png)

Seoul-TGW-Attach-Seoul-VPC-HQ를 선택하면, 이미 "Seoul-VPC-HQ"의 TGW-Subnet ID에 연결되어 있는 것을 확인할 수 있습니다. 또한 Routing Table에 Association 된 상태도 확인이 가능합니다.

1. **TGW Routing Table과 Attachment가 연결된 상태를 확인**
2. **Attachment가 VPC의 어떤 Subnet과 연결되었는지 확인**

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2849%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2849%29.png)

아래에서 나머지 VPC들도 선택해서 확인해 봅니다.

```text
Seoul-TGW-Attach-Seoul-VPC-STG
Seoul-TGW-Attach-Seoul-VPC-DEV
Seoul-TGW-Attach-Seoul-VPC-PRD
```

#### 

#### 

#### Task6. TGW Routing Table 확인.

**`VPC-Transit Gateway-Transit Gateway- Transit Gateway 라우팅 테이블`** 을 선택해서 라우팅 테이블 구성을 확인해 봅니다. 라우팅 테이블은 2개로 구성되어 있습니다.

East-To-West 트래픽을 위한 라우팅 테이블 도메인, North-To-South 트래픽을 위한 라우팅 테이블 도메인으로 구성되어 있습니다. Seoul-VPC-HQ 는 North-To-South 라우팅 테이블 도메인에 속해 있습니다.

**먼저 North-To-South 라우팅 테이블 도메인을 확인합니다.**

**해당 라우팅 테이블 도에인에는 Seoul-VPC-HQ를 연결했습니다.**

Associations와 Propagation 탭을 눌러서, Seoul-VPC-HQ 연결과 Seoul-VPC-HQ의 CIDR가 정상적으로 업데이트 되었는지 확인합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2824%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2824%29.png)

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2859%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2859%29.png)

propagation이 정상적으로 구성되었기 때문에 Route 탭을 선택하면, Route Type은 Propagated 되었다고 표기됩니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2871%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2871%29.png)

**이제 East-To-West 라우팅 테이블 도메인을 확인합니다.**

**해당 라우팅 테이블 도에인에는 Seoul-VPC-PRD, Seoul-VPC-STG, Seoul-VPC-DEV를 연결했습니다.**

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2866%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2866%29.png)

**East-To-West Routing Table 도메인을 선택하여, 라우팅 테이블 속성을 확인합니다. Association 탭을 선택해서 3개의 VPC가 Association 되었는지 확인합니다.**

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2823%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2823%29.png)

Propagations 탭을 선택해서, 3개의 VPC CIDR를 Propagation 하는지 확인합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2844%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2844%29.png)

Routing 탭을 선택해서, 앞서 Propagation 된 Route가 정상적으로 등록되었는지 확인합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2842%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2842%29.png)

**Cloudformation을 통해서 모두 정상적으로 구성되었습니다.**

해당 라우팅테이블 도메인에는 Seoul-VPC-PRD, Seoul-VPC-STG, Seoul-VPC-DEV만 연결되어 있습니다.

### 

### 3. TGW 기반 트래픽 제어

#### 

#### Task7. SSM 에서 인스턴스 확인

모든 랩의 구성 시험은 Private 인스턴스로 시험합니다. Cloudformation을 통해 System Manager와 Session Manager를 사용할 수 있도록 자동 배포 구성하였습니다.

Session Manager를 사용할 수 있도록 아래 같이 각 PC환경에 맞추어서 AWS Session Manager Plugin을 설치합니다. Cloud9을 사용하거나 웹콘솔에서 Session Manager를 사용하면 각 PC환경에서 설치할 필요가 없습니다.

{% hint style="info" %} PC 환경에서는 사전에 반드시 AWS CLI를 설치합니다. Cloud9으로 사용할 때는 별도 구성하지 않아도 됩니다. {% endhint %}

**Windows Session manager plugin 설치 \(Cloud9, 웹기반 세션 매니저 사용시 생략\)**

```text
https://s3.amazonaws.com/session-manager-downloads/plugin/latest/windows/SessionManagerPluginSetup.exe
```

**Mac OS용 Session manager plugin 설치\(Cloud9, 웹기반 세션 매니저 사용시 생략\)**

번들 설치 관리자를 다운로드합니다.

```text
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/mac/sessionmanager-bundle.zip" -o "sessionmanager-bundle.zip"
```

패키지의 압축을 풉니다.

```text
unzip sessionmanager-bundle.zip
```

설치 명령을 실행합니다.

```text
sudo ./sessionmanager-bundle/install -i /usr/local/sessionmanagerplugin -b /usr/local/bin/session-manager-plugin
```

**Fedora Linux 에서 Session Manager Plugin 설치\(Cloud9, 웹기반 세션 매니저 사용시 생략\)**

```text
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/linux_64bit/session-manager-plugin.rpm" -o "session-manager-plugin.rpm"
sudo yum install -y session-manager-plugin.rpm
```

**Ubuntu에서 Session Manager Plugin 설치\(Cloud9, 웹기반 세션 매니저 사용시 생략\)**

```text
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" -o "session-manager-plugin.deb"
sudo dpkg -i session-manager-plugin.deb
```

**아래와 같이 Cloud9에서 shell을 실행해 봅니다.**

```text
~/environment/buildernet/aws_ec2_ext.sh |grep "Seoul-VPC-HQ"
~/environment/buildernet/aws_ec2_ext.sh |grep "Seoul-VPC-PRD"
~/environment/buildernet/aws_ec2_ext.sh |grep "Seoul-VPC-STG"
~/environment/buildernet/aws_ec2_ext.sh |grep "Seoul-VPC-DEV"

```

실행한 예제입니다.

```text
~/environment/buildernet/aws_ec2_ext.sh | grep "Seoul-VPC-HQ"
|  Seoul-VPC-HQ-Public-10.0.12.102  |  ap-northeast-2b |  i-0aa1c4c96f8924b33 |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.12.102  |  3.34.195.174    |
|  Seoul-VPC-HQ-Private-10.0.22.101 |  ap-northeast-2b |  i-078a899d467028886 |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.22.101  |  None            |
|  Seoul-VPC-HQ-Private-10.0.22.102 |  ap-northeast-2b |  i-0a7398a27be7d07f4 |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.22.102  |  None            |
|  Seoul-VPC-HQ-Public-10.0.12.101  |  ap-northeast-2b |  i-02717f54153d127e0 |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.12.101  |  15.164.173.76   |
|  Seoul-VPC-HQ-Private-10.0.21.101 |  ap-northeast-2a |  i-0c46e31a566a5770a |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.21.101  |  None            |
|  Seoul-VPC-HQ-Public-10.0.11.101  |  ap-northeast-2a |  i-03a97a2e31d5097f3 |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.11.101  |  13.125.16.126   |
|  Seoul-VPC-HQ-Public-10.0.11.102  |  ap-northeast-2a |  i-060471cb2d82942a9 |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.11.102  |  3.35.220.15     |
|  Seoul-VPC-HQ-Private-10.0.21.102 |  ap-northeast-2a |  i-0f36233e7389fec7a |  t3.small |  ami-006e2f9fa7597680a |  running |  10.0.21.102  |  None            |

```

ssm plugin을 통해서 인스턴스 ID 기반으로, 직접 Private Instance에 접속합니다. 인스턴스 ID는 \*\*`"aws_ec2.sh"`\*\*을 통해 확인 할 수 있습니다.

아래와 같은 명령을 통해서 직접 4개의 Private Instance에 접속합니다. \(10.0.21.101, 10.1.21.101, 10.2.21.101, 10.2.31.101\)

* **Seoul-VPC-HQ-Private-10.0.21.101**
* **Seoul-VPC-PRD-Private-10.1.21.101**
* **Seoul-VPC-STG-Private-10.2.21.101**
* **Seoul-VPC-DEV-Private-10.3.21.101**

```text
aws ssm start-session --target "인스턴스 ID"
```

Cloud9에서 터미널 창을 4개를 추가로 오픈하고, 아래와 같이 각 4개의 호스트에 명령을 입력하여, bash 콘솔로 접속하고, 시험할 호스트들을 host file에 등록합니다.

* **Seoul-VPC-HQ-Private-10.0.21.101**
* **Seoul-VPC-PRD-Private-10.1.21.101**
* **Seoul-VPC-STG-Private-10.2.21.101**
* **Seoul-VPC-DEV-Private-10.3.21.101**

```text
sudo -s
echo 10.0.21.101 SEOUL-VPC-HQ-Private >> /etc/hosts 
echo 10.1.21.101 SEOUL-VPC-PRD-Private >> /etc/hosts
echo 10.2.21.101 SEOUL-VPC-STG-Private >> /etc/hosts
echo 10.3.21.101 SEOUL-VPC-DEV-Private >> /etc/hosts
echo 10.4.21.101 SEOUL-VPC-PRT-Private >> /etc/hosts
echo 10.5.21.101 IAD-VPC-Private >> /etc/hosts

```

#### 

#### Task8. 시나리오 이해하기

**다음과 같은 시나리오 구성으로 Task9~11를 수행합니다.**

**1.빌더스 컴퍼니는 아래와 같은 VPC를 하나의 계정에 소유하고 있습니다.**

* **IT Control Tower : Seoul-VPC-HQ**
* **Production Workload : Seoul-VPC-PRD**
* **Staging Workload : Seoul-VPC-STG**
* **Dev Workload : Seoul-VPC-Dev**

**2.STG,DEV 간의 개발 작업 종료 후 잦은 네트워크 연결이 필요합니다.**

**3.STG,DEV 완료 후에 잠시 동안 PRD와 연결이 필요합니다.**

**4. PRD 가 개시되기 직전에 인터넷을 차단하고, HQ를 통해서 인터넷에 연결되며 보안을 강화합니다.**

**목표 구성과 필요작업은 아래와 같습니다.**

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2837%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2837%29.png)

#### 

#### 

#### Task9. Staging과 Dev 연결

Seoul-VPC-STG와 Seoul-VPC-DEV를 TGW를 통해 연결 구성해 봅니다.

East-To-West에는 이미 Seoul-VPC-STG, Seoul-VPC-DEV의 CIDR가 Propagated 되어 있기 때문에, TGW에서 작업은 불필요합니다. 하지만 각 VPC에서 라우팅 테이블이 구성되어 있지 않기 때문에 상호간 연결되지 않습니다.

아래 명령을 통해 각 Cloud9 터미널 콘에서 Ping 시험을 해 봅니다.

{% hint style="info" %} Cloudformation을 통해 Security Group은 시험에 필요한 트래픽은 모두 허용되어 있습니다. {% endhint %}

```text
##Seoul-VPC-STG-Private-10.2.21.101
ping SEOUL-VPC-DEV-Private

```

```text
##Seoul-VPC-DEV-Private-10.3.21.101
ping SEOUL-VPC-STG-Private
```

{% hint style="info" %} 상호간의 트래픽이 허용되지 않습니다. 각 VPC에서 라우팅 테이블이 없기 때문입니다. {% endhint %}

VPC- 가상 프라이빗 클라우드 - 라우팅 테이블에서 아래 라우팅 테이블 Tag 확인하고, 수정합니다.

```text
Seoul-VPC-STG-Private-Subnet-A-RT
```

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2816%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2816%29.png)

```text
Seoul-VPC-DEV-Private-Subnet-A-RT
```

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2828%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2828%29.png)

이제 다시 앞서 실행한 각 인스턴스에서의 Ping이 정상적으로 처리되는 지 확인합니다.

{% hint style="success" %} **이제 Dev환경에서 Stage환경으로 연결이 되었습니다.** {% endhint %}

#### 

#### 

#### Task10. Production 연결

Dev, Stage 환경에서 모든 준비가 완료되고 필요 요구에 따라 Production으로 연결이 필요하게 되었습니다.

앞서 Task7 과 유사하게 Production에서 라우팅 테이블만 변경하면 Production, Staging , Dev는 모두 연결 됩니다.

아래 명령을 통해 각 Cloud9 터미널 콘솔에서 Ping 시험을 해 봅니다.

{% hint style="info" %} Cloudformation을 통해 Security Group은 시험에 필요한 트래픽은 모두 허용되어 있습니다. {% endhint %}

```text
##Seoul-VPC-PRD-Private-10.1.21.101
ping SEOUL-VPC-DEV-Private

```

```text
##Seoul-VPC-PRD-Private-10.1.21.101
ping SEOUL-VPC-STG-Private
```

{% hint style="info" %} 상호간의 트래픽이 허용되지 않습니다. 각 VPC에서 라우팅 테이블이 없기 때문입니다. {% endhint %}

**`VPC- 가상 프라이빗 클라우드 - 라우팅 테이블`** 에서 아래 라우팅 테이블 Tag 확인하고, 수정합니다.

```text
Seoul-VPC-PRD-Private-Subnet-A-RT
```

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2865%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2865%29.png)

이제 다시 앞서 실행한 각 인스턴스에서의 Ping이 정상적으로 처리되는 지 확인합니다.

{% hint style="success" %} **이제 Production과 Dev,Staging 환경이 연결되었습니다.** {% endhint %}

{% hint style="warning" %} Production과 Staging간의 10.2.21.101만 잠시 Block 하고 싶습니다. 어떻게 해야 할까요? {% endhint %}

Transit Gateway에는 Blackhole 기능이 있습니다. 이것은 전통적인 네트워크 장비에서 Null Routing과 유사합니다. 특정 라우팅테이블을 블랙홀에 빠뜨려서, 격리시키는 방식으로 Transit Gateway 를 통과시키지 못하도록 합니다.

**`VPC- TransitGateway-TransitGateway` 라우팅 테이블을 선택합니다.**

**`Seoul-TGW-RT-East-To-West`** RouteTable을 선택하고, **`Route`** 탭을 선택합니다. **`Create Static Route`** 버튼을 누르고 아래와 같이 Staging Host만 격리 시켜 봅니다.

```text
###Blcokhole Target host
10.2.21.101/32
```

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2822%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2822%29.png)

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2826%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2826%29.png)

**다시 Blackhole을 해제합니다.**

#### 

#### 

#### Task11. Production과 HQ 연결

이제 모든 작업이 완료되고, Production 개시 단계입니다. 보안 강화를 위해 SEOUL-HQ-VPC를 통해서 외부 연결을 하도록 합니다.

먼저 TGW East-To-West 라우팅테이블에 아래와 같이 Seoul-VPC-HQ로 트래픽 경로를 추가합니다.

**`VPC-Transit Gateway-Transit Gateway 라우팅테이블`** 을 선택합니다.

**`Seoul-TGW-RT-East-To-West`** 를 선택하고, **`Route`** 탭을 선택합니다.

\*\*`Create Static Route`\*\*를 선택하고, 0.0.0.0/0에 대한 경로를 Seoul-TGW-Seoul-VPC-HQ Attachment 추가합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2854%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2854%29.png)

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2847%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2847%29.png)

이제 Seoul-VPC-PRD의 Private Subnet 라우팅에서 인터넷으로 가는 목적지를 NAT Gateway에서 Transit Gateway로 아래와 같이 변경합니다.

```text
Seoul-VPC-PRD-Private-Subnet-A-RT
```

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%288%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%288%29.png)

**Seoul-VPC-PRD-Private-10.1.21.101 인스턴스에서** [**www.aws.com**](http://www.aws.com) **으로 ping을 실행해 봅니다.**

{% hint style="warning" %} **실행되지 않습니다. 이유는 간단합니다. Return 되는 경로가 Seoul-VPC-HQ에서 설정되어 있지 않습니다. NAT Gateway가 있는 Public Routing Table에서 Seoul-VPC-PRD-Private 에 대한 라우팅 경로를 추가해야 합니다. 또한 Seoul-VPC-HQ 가 연결되어 있는 Transit Gateway North-To-South에서 라우팅도 추가를 하면 정상적으로 연결됩니다.** {% endhint %}

**`VPC- 가상 프라이빗 클라우드 - 라우팅 테이블`** 에서 아래 라우팅 테이블 Tag 확인하고, 수정합니다.

```text
Seoul-VPC-HQ-PublicRT
```

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image.png)

TGW North-To-South 라우팅테이블에 아래와 같이 Seoul-VPC-HQ로 트래픽 경로를 추가합니다.

**`VPC-Transit Gateway-Transit Gateway 라우팅테이블`** 을 선택합니다.

**`Seoul-TGW-RT-North-To-South`** 를 선택하고, **`Route`** 탭을 선택합니다.

\*\*`Create Static Route`\*\*를 선택하고, 10.1.21.0/24 에 대한 경로를 Seoul-TGW-Seoul-VPC-PRD Attachment 추가합니다.

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2815%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2815%29.png)

**이제 Seoul-VPC-PRD-Private-10.1.21.101 인스턴스에서 정상적으로 외부 접속이 되는지 확인해 봅니다.**

{% hint style="success" %} Transit Gateway 구성에 대한 모든 실습을 마쳤습니다. 연결되는 다음 Chapter를 사용하지 않을 경우, Cloudformation에서 Stack을 **Seoul-TGW.ym**l 부터 삭제하고, **나머지 VPC yml을 삭제**하면 모든 자원이 삭제 됩니다. {% endhint %}

### 

### TransitGateway 구성하기 과정

**1.4개의 VPC 생성**

* Cloudformation 을 통해서, 다운로드 받은 4개의 yaml 파일 업로드 \(Seoul-VPC-HQ, Seoul-VPC-PRD, Seoul-VPC-STG, Seoul-VPC-DEV\)하고 , VPC 생성

**2.서울 리전\(ap-northeast-2\)에서 TransitGateway 생성**

* Cloudformation을 통해서, 다운로드 받은 yaml 파일 업로드\(Seoul-TGW\)하고, TGW 생성

**3.생성된 VPC, EC2 구성 확인.**

**4. TGW 구성확인**

**5. TGW Attachment \(연결\) 확인**

**6. TGW Routing Table 확인**

**7. Session Manager를 통해서 EC2 인스턴스 접속하기**

**8. 시나리오 이해하기**

**9. Staging VPC와 Dev VPC 연결하기 - TGW를 통해 연결하**

**10. Production 연결하기 - TGW 연결하기.**

**11. Production과 HQ 연결하기 - 서로 다른 라우팅 테이블에서 연결하기.**

**해당 LAB의 질문 사항은 whchoi98@gmail.com/ whchoi@amazon.com 또는🙋♂ 슬랙채널\(https://whchoi-hol.slack.com/ , https://join.slack.com/t/whchoi-hol/shared\_invite/zt-necc66t1-n6pSgrVfGW1w6SLAQUTP8A\) \#aws-builders-adv-networking-hol 에서 문의 가능합니다.**

