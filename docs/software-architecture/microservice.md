---
layout: default
title: Micro Service
parent: Software Architecture
nav_order: 2
---

# Micro Service
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## **Micro Service 란?**
- Application Logic 을 책임이 명확한 작은 Component 로 분해하고 이들을 조합해서 Solution 을 제공
- 각 Component 는 완전히 상호 독립적이며, 여러 Application 에서 재사용 가능
- HTTP, JSON 같은 경량 통신 Protocol 을 사용하므로, 각 Component 는 다양한 언어와 기술로 구축 가능


**>>> 작고(small) 단순하며(simple) 분리된(decoupled) 서비스**
{: .no_toc .text-gamma }


* * *


## **Micro Service의 특징**
- 유연성(Flexible) : 새로운 기능을 신속하게 제공할 수 있음. 코드 변경에 따른 복잡성이 낮고, 코드 배포 테스트 시간이 줄어듬
- 회복성(resilient) : 애플리케이션 한 부분의 저하로 전체가 망가지지 않음. 회복 불능 에러가 발생했을 때도 애플리케이션이 원활하게 기능 저하됨
- 확장성(scalable) : 수평적 분산 가능. 작은 서비스를 확장하는 것은 국지적이며 비용 효율이 높음


**>>> 확장 가능하고(scalable) 회복적이며(resilient) 유연한(flexible) 애플리케이션**
{: .no_toc .text-gamma }


* * *


## **Micro Service 개발 시 고려해야할 사항**
- 적정 크기 : Micro Service 가 과도한 책임을 맡지 않고 한가지 책임 영역에 집중하도록 해야 함
- 위치 투명성 : Service Client 에 영향을 주지 않고 Serivice Instance 를 추가, 삭제 할 수 있도록 서비스 호출에 대한 물리적 상세 정보를 관리해야 함
- 회복성 : 장애가 발생한 Service를 우회하고 ‘빨리 실패’ 하는 방법을 사용해 Application 의 전반적 무결성을 유지해야 함
- 반복성 : 새로운 Service Instance 가 시작할 때마다 기존 Instance 와 동일한 코드와 구성을 유지할 수 있어야 함
- 확장성 : Service 간 의존성을 최소화하면서 Micro Service 를 원만하게 확장할 수 있어야 함


* * *


## **Micro Service 개발 패턴**


### **핵심 개발 패턴 : Micro Service 구축에 대한 기본 사항 결정**
- 서비스 세분성 : Service 가 담당해야할 적정한 책임 수준 결정
- 통신 프로토콜(protocol) : Client 와 Service 가 데이터를 교환하는 방법
- 인터페이스 설계(interface) : Service Endpoint 를 Client 에 공개하는 방법
- 구성 관리(configuration) : Service 가 Application 별 구성을 관리해 코드와 구성이 독립적인 개체가 되는 방법
- 이벤트 프로세싱(event processing) : Event 를 사용해 서비스 간 상태 및 데이터 변경 사항을 통신하는 방법


### **라우팅 패턴 : Micro Service 가 위치를 발견하고 라우팅하는 방법**
- 서비스 디스커버리(service discovery) : Client 에서 Service 의 물리적 위치를 추상화. 새로운 Micro Service Instance 가 추가되고 비정상 Service Instance 는 투명하게 서비스에서 제거
- 서비스 라우팅(service routing) : 보안 정책과 라우팅 규칙을 여러 Service 와 Service Instance 에 차별 없이 적용하기 위한 단일 진입점 제공


### **클라이언트 회복성 패턴 : 1개의 Service 문제가 연쇄적으로 발생하지 않도록 방지**
- 클라이언트 부하 분산(client-side load balancing) : Micro Service Endpoint 를 캐시하고 Instance 간 Service 호출 부하를 분산
- 회로차단기(circuit breakers) : Service Client 가 실패한 Service 를 반복적으로 호출하지 않도록 함
- 폴백(fallback) : Service 호출이 실패할 때 대체 수단을 사용해 작업을 수행할 수 있도록 함
- 벌크헤드(bulkhead) : 오작동하는 Service 호출 하나가 모든 Resource 를 차지하지 않도록 Client 에서 격리함


### **보안 패턴 : Token 기반 보안 표준을 사용하는 방법**
- 인증(authentication) : Service Client 가 누구인지 알게 함
- 인가(authorization) : Service Client 가 작업을 수행할 자격이 있는지 알게 함
- 자격 증명 관리(credential management) 및 전파(propagation) : Service Client 가 한 Transaction 과 관련된 여러 Service 호출에서 항상 자격 증명을 제시하지 않아도 될 방법


### **로깅 및 추적 패턴 : Micro Service 에서 디버깅과 추적을 하는 방법**
- 로그 상관관계(log correlation) : 단일 Transaction 에 대해 여러 Service 간 생성된 모든 Log 를 연결하는 상관관계 ID를 구현하는 방법
- 로그 수집(log aggregation) : 모든 Service Instance 에서 생성된 Log 를 단일 Database 로 수집하는 방법
- 마이크로서비스 추적(micro service tracing) : Client Transaction 을 시각화하고 Service 의 성능 특성을 이해하는 방법


### **빌드 및 배포 패턴 : 구성 편차를 없애는 방법**
- 빌드 및 배포 파이프라인 : One-Click 빌드와 배포를 강조하는 반복 가능한 빌드 및 배포 프로세스를 만드는 방법
- 코드형 인프라스트럭처 : Source 제어를 사용해 실행하고 관리할 수 있는 코드로 Service Provisioning 을 처리하는 방법
- 불변 서버(immutable servers) : Micro Service 이미지를 생성하고 배포한 후 변경하지 못하도록 하는 방법
- 피닉스 서버(phoenix servers) : Micro Service 를 실행하는 서버를 정기적으로 해체하고 불변 이미지를 재생성하는 방법


* * *


## **Spring Cloud 와 Micro Service 개발 패턴 Mapping**


### **핵심 개발 패턴 Mapping**
- Spring Boot : REST 기반 Interface 구축, JSON Protocol 직렬화 등
- Spring Cloud Config : Application 구성 데이터 관리, Git 등과 통합 가능
- Spring Cloud Stream : 경량 Message Processing, 여러 메시지 브로커와 통합 가능
- Kafka : Java 로 구현된 비동기적 메시지 버스
- RabbitMQ : 비동기 메시지 플랫폼


### **라우팅 패턴 Mapping**
- Spring Cloud Netflix Eureka : Service Instance 등록과 말소 처리, Server 가 배포된 물리적 위치를 추상화
- Spring Cloud Netflix Zuul : Service Request Proxy 하는 Service Gateway. 보안 인가 및 인증, 콘텐츠 필터링, 라우팅 규칙 등 표준 서비스 정책 시행


### **클라이언트 회복성 패턴 Mapping**
- Spring Cloud Netflix Ribbon : Eureka 를 통합할 뿐 아니라 Service 호출에 대한 클라이언트 부하 분산 기능 제공
- Spring Cloud Netflix Feign : 인터페이스 방식으로 REST 서비스 호출을 구현
- Spring Cloud Netflix Hystrix : 회로 차단기, 폴백, 벌크헤드 구현 


### **보안 패턴 Mapping**
- Spring Cloud Security : Token 기반 인증 및 인가 Framework. OAuth2 Token 및 JWT 지원


### **로깅 및 추적 패턴 Mapping**
- Spring Cloud Sleuth : HTTP Request 와 Message Channel 에 고유 추적 식별자를 통합하여 여러 Service 를 순회하는 Transaction 을 추적할 수 있음
- Papertrail : 클라우드 기반 로깅 플랫폼
- Kibana : ELK 스택을 이용한 로깅 플랫폼
- Zipkin : 로깅 시각화를 지원하는 분산 추적 플랫폼 


### **빌드 및 배포 패턴 Mapping**
- Travis : Git 과 쉽게 연동할 수 있는 빌드 및 배포 Pipeline
- Bitbucket Pipeline : Bitbucket 기반 빌드 및 배포 Pipeline
- Docker : 불변 서버와 피닉스 서버를 구현하기 위한 서버 이미지 컨테이너

