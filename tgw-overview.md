---
description: 'Update: 2021-09-09'
---

# TransitGateway 소개

## 1.AWS Transit Gateway 소개

AWS Transit Gateway는 중앙 허브를 통해 VPC와 온프레미스 네트워크를 연결합니다. 복잡한 피어링 관계를 제거하여 네트워크를 간소화합니다. 클라우드 라우터 역할을 하므로 새로운 연결을 한 번만 추가하면 됩니다.

글로벌 확장 시 리전 간 피어링을 사용하면 [AWS 글로벌 네트워크](https://aws.amazon.com/ko/about-aws/global-infrastructure/)에서 AWS Transit Gateway를 하나로 연결할 수 있습니다.. 데이터는 자동으로 암호화되고 퍼블릭 인터넷을 통하지 않습니다. 중앙 위치에 있으므로 [AWS Transit Gateway 네트워크 관리자](https://aws.amazon.com/ko/transit-gateway/network-manager/)를 사용하여 전체 네트워크를 보고 SD-WAN\(소프트웨어 정의 광역 네트워크\) 디바이스에 연결할 수 있습니다.

## 2.AWS Transit Gateway 사용 이점

**간편한 연결**

AWS Transit Gateway는 클라우드 라우터 역할을 하므로 네트워크 아키텍처가 간소화됩니다. 네트워크 확장 시 증가하는 연결 관리로 인한 복잡성이 발생하지 않습니다. 글로벌 애플리케이션을 구축하는 경우 리전 간 피어링을 사용하여 AWS Transit Gateway를 연결할 수 있습니다.

**가시성 및 제어 향상**

AWS Transit Gateway 네트워크 관리자를 사용하면 중앙 콘솔에서 Amazon VPC 및 엣지 연결을 손쉽게 모니터링할 수 있습니다. 주요 SD-WAN 디바이스와 통합되므로 AWS Transit Gateway 네트워크 관리자를 사용하여 문제를 빠르게 식별하고 글로벌 네트워크의 이벤트에 대응할 수 있습니다.

**향상된 보안**

Amazon VPC와 AWS Transit Gateway 간의 트래픽은 AWS의 글로벌 프라이빗 네트워크에서 유지되며 퍼블릭 인터넷에 노출되지 않습니다. AWS Transit Gateway 리전 간 피어링은 단일 장애 발생 지점이나 대역폭 병목 없이 모든 트래픽을 암호화합니다. 따라서 DDoS\(분산 서비스 거부\) 공격 및 기타 일반적인 익스플로잇을 차단하는 데 도움이 됩니다.

**유연한 멀티캐스트**

AWS Transit Gateway 멀티캐스트 지원은 동일한 콘텐츠를 다수의 특정 대상으로 분산합니다. 광범위한 온프레미스 멀티캐스트 네트워크가 필요하지 않으며 화상 회의, 미디어 또는 전화 회의와 같은 처리량이 많은 애플리케이션에 필요한 대역폭이 감소합니다.

## 3. 네트워크 간소화 디자인

**AWS Transit Gateway 미사용 시**

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2831%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2831%29.png)

확장할 때마다 복잡성이 증가합니다. 각 VPC 안에 라우팅 테이블을 유지해야 하고 개별 네트워크 게이트웨이를 사용하여 각 온사이트 위치에 연결해야 합니다.

**AWS Transit Gateway 사용 시**

[![](https://github.com/whchoi98/builders20210312/raw/master/.gitbook/assets/image%20%2874%29.png)](https://github.com/whchoi98/builders20210312/blob/master/.gitbook/assets/image%20%2874%29.png)

네트워크가 간소화되고 확장성이 개선됩니다. AWS Transit Gateway가 각 VPC 또는 VPN 간의 모든 트래픽을 라우팅하므로, 단일 위치에서 모든 트래픽을 관리하고 모니터링할 수 있습니다.

