---
description: 'Update : 2021-09-09'
---

# TransitGateway 모니터링

## 1.네트워크 매니저 소개

Transit Gateway Network Manager\(Network Manager\)를 사용하면 전송 게이트웨이를 중심으로 구축된 네트워크를 중앙에서 관리할 수 있습니다. AWS 리전 및 온프레미스 위치에서 글로벌 네트워크를 시각화하고 모니터링할 수 있습니다.

* **글로벌 네트워크** — 네트워크 객체의 상위 수준 컨테이너 역할을 하는 단일 프라이빗 네트워크입니다.
* **디바이스** — 온프레미스 네트워크, 데이터 센터, AWS 클라우드 또는 기타 클라우드 공급자의 물리적 또는 가상 어플라이언스를 나타냅니다.
* **연결** — 두 디바이스 간의 연결을 나타냅니다. 물리적 또는 가상 어플라이언스와 VPC 내 타사 가상 어플라이언스 간의 연결이거나 온프레미스 네트워크의 물리적 어플라이언스 간의 연결일 수 있습니다.
* **링크** — 사이트로부터의 단일 인터넷 연결을 나타냅니다.
* **사이트** — 물리적 온프레미스 위치를 나타냅니다. 지점, 사무실, 매장, 캠퍼스 또는 데이터 센터가 될 수 있습니다.

## 2.네트워크 매니저 구성

Task1. 네트워크 매니저 생성

**`VPC - Transit Gateway - 네트워크 관리자`** 를 선택하고, **`Create a Global Network`** 를 선택합니다.

![](.gitbook/assets/image%20%2843%29.png)

글로벌 네트워크 생성에서 아래 내용을 설정하고, 글로벌 네트워크를 생성합니다.

* 이름 - 글로벌 네트워크 식별을 위한 이름을 정의합니다.

```text
MyNetwork
```

![](.gitbook/assets/image%20%2833%29.png)

정상적으로 생성되었는지 확인하고, 생성된 **`글로벌 네트워크를 선택`**합니다.

![](.gitbook/assets/image%20%2832%29.png)

**`전송 게이트웨이 등록`**을 선택합니다.

![](.gitbook/assets/image%20%2812%29.png)

앞서 만들어 둔 TransitGateway를 선택하고, 전송게이트웨이 등록을 클릭합니다.

![](.gitbook/assets/image%20%2827%29.png)

전송 게이트웨이가 정상적으로 등록되었는지 확인합니다.

![](.gitbook/assets/image%20%284%29.png)

Network Manager 좌측 대시보드의 **`대시보드`** 메뉴를 선택하고, 전송게이트웨이가 2개 등록되었는지 확인합니다. 또한 전송게이트웨이의 Connection 상태를 확인합니다.

![](.gitbook/assets/image%20%28100%29.png)

이제 메뉴들을 살펴 봅니다. 먼저 지리적 메뉴를 선택하고 Geo Map 상태를 살펴봅니다.

![](.gitbook/assets/image%20%2887%29.png)

토폴로지를 선택하고, 논리적 구성도를 확인합니다.

![](.gitbook/assets/image%20%2826%29.png)

### 

## 3. 경로 분석기 확인

글로벌 네트워크에서 Route Analyzer를 사용하여 전송 게이트웨이 라우팅 테이블의 라우팅 분석을 수행할 수 있습니다. Route Analyzer는 지정된 소스와 대상 간의 라우팅 경로를 분석하고 구성 요소 간 연결에 대한 정보를 반환합니다. Route Analyzer를 사용하여 다음을 수행할 수 있습니다.

앞서 생성한 소스와 대상들을 입력해서 분석 결과를 살펴 봅니다.

소스

* 전송 게이트 웨이 : Seoul-TGW
* 전송 게이트웨이 연결 : Seoul-TGW-Attach-Seoul-VPC-PRD
* IP 주소 : 10.1.21.101

대상

* 전송 게이트 웨이 : Seoul-TGW
* 전송 게이트웨이 연결 : Seoul-TGW-Attach-Seoul-VPC-DEV
* IP 주소 : 10.3.21.101

![](.gitbook/assets/image%20%2853%29.png)

경로 분석기 결과를 확인해 봅니다.

![](.gitbook/assets/image%20%2877%29.png)

**해당 LAB의 질문 사항은 whchoi98@gmail.com/ whchoi@amazon.com 또는🙋 슬랙채널\(https://whchoi-hol.slack.com/ , https://join.slack.com/t/whchoi-hol/shared\_invite/zt-necc66t1-n6pSgrVfGW1w6SLAQUTP8A\) \#aws-builders-adv-networking-hol 에서 문의 가능합니다.**

