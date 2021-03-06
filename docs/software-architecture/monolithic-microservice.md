---
layout: default
title: Monolithic Vs Micro Service
parent: Software Architecture
nav_order: 3
---

# Monolithic Vs Micro Service
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## **Monolithic Architecture 의 장 / 단점**

### **모놀리식 아키텍처 란?**
- 시스템을 하나의 실행 / 배포 가능한 컴포넌트로 구성하는 소프트웨어 아키텍처
- 구현 뷰를 단일 컴포넌트(하나의 실행 파일이나 WAR 파일)로 구성한 아키텍처 스타일


### **모놀리식 아키텍처 의 장점**
- 개발이 간단하다  
- 애플리케이션을 쉽게 변경할 수 있다
- 테스트하기 쉽다
- 배포하기 쉽다 
- 확장하기 쉽다


**> 단, 비교적 규모가 작았던 초기 단계에만 해당한다 !!!**
{: .no_toc .text-gamma }


### **모놀리식 아키텍처 의 단점**
- 개발이 더디다 : IDE 실행 속도 및 빌드 시간이 더디다
- 커밋부터 배포까지 과정이 험난하다 : 테스트 및 코드 안정화가 오래 걸린다
- 확장하기 어렵다 : 모듈마다 리소스 요건이 상이하여 서버 구성 시 리소스 배분에 신경써야 한다
- 신뢰성이 부족하다 : 테스트성이 부족하고 결함 격리가 되지 않아 치명적인 버그가 발생할 가능성이 높다
- 낡은 기술 스택에 의존한다 : 최신 기술을 사용하려면 전체 애플리케이션을 재작성해야 한다


**> 애플리케이션은 시간이 흐를수록 덩치가 커질 수 밖에 없다 !!!**
{: .no_toc .text-gamma }


* * *


## **Micro Services Architecture 의 장 / 단점**


### **마이크로서비스 아키텍처 의 특징**
- 하나의 애플리케이션을 여러 서비스로 기능 분해하는 아키텍처 스타일
- 구현 뷰를 다수의 컴포넌트(여러 실행 파일이나 WAR 파일)로 구성한 아키텍처 스타일
- 애플리케이션을 느슨하게 결합된, 독립적으로 배포 가능한 여러 서비스로 구성
- 서비스를 모듈성 modularity 의 단위로 사용
- 서비스마다 자체 DB 를 소유


**> 경계 컨텍스트 bounded context 가 있는, 느슨하게 결합된 엘리먼트 element 로 구성된 서비스 지향 아키텍처 Service Oriented Architecture**
{: .no_toc .text-gamma }


### **마이크로서비스 아키텍처의 장점**
- 테스트성, 배포성, 자율성이 높아 지속적 전달 / 배포 continuous delivery / deployment 가 가능하다 
- 서비스가 작아 관리하기 용이하다
- 서비스를 독립적으로 배포 / 확장 할 수 있다
- 결함 격리가 잘된다
- 신기술을 시험 / 도입하기 쉽다


### **마이크로서비스 아키텍처의 단점**
- 시스템을 분해하는 것이 쉽지 않다 : 분산 모놀리스 시스템을 구축하게 될 가능성이 있다
- 분산 시스템은 복잡하다 : 개발, 테스트, 배포가 어렵다
- 여러 서비스에 걸친 공통 기능을 배포할 때 세심한 조율이 필요하다
- 도입 시기를 결정하기 어렵다 : 개발 초기에는 모놀리식 아키텍처에 장점이 있다


* * *


**cf.마이크로서비스 아키텍처 vs. 서비스 지향 아키텍처**
{: .no_toc .text-gamma }

<div class="code-example" markdown="1">

| 구분        | SOA                                                                      | MSA                                                         |
|:----------|:-------------------------------------------------------------------------|:------------------------------------------------------------|
| 기술 스택    | SOAP, WS 표준 등 무거운 프로토콜을 응용한 엔터프라이즈 서비스 버스 ESB 중심의 스마트 파이프 smart pipe 를 활용 | REST 나 gRPC 처럼 가벼운 프로토콜을 응용한 메시지 브로커 나 덤 파이프 dumb pipe 를 활용 |
| 데이터 처리 방식 | 전역 데이터 모델링 및 DB 공유                                                       | 서비스 개별 데이터 모델 및 DB                                          |
| 서비스 크기    | 크고 복잡한 모놀리식 애플리케이션을 통합 / 덩치 큰 서비스 몇 개로 구성                                | 소규모 서비스 / 수십 ~ 수백 개의 서비스로 구성                                |

</div>

* * *


**cf.마이크로서비스 성공 요소**
- 8 ~ 12명으로 구성된 범기능 팀으로 조직 구성 : 각 팀이 서비스를 독자적으로 개발, 테스트, 배포
- 애자일 개발 프로세스를 도입하고 스크럼, 칸반 등을 실천
- 소프트웨어를 언제라도 릴리스 release 하고 프로덕션에 자동 배포하는 지속적 전달 / 배포 를 구축


* * *


**cf.지속적 전달 / 배포의 비즈니스 이점**
- 제품을 시장에 내놓는 시기를 앞당길 수 있고, 고객 피드백에 신속히 대응할 수 있다
- 현재 고객들이 기대하는 수준으로 확실하게 서비스를 제공할 수 있다
- 직원들이 제품의 가치를 전달하는 데 더 많은 시간을 투자할 수 있어 만족도가 높다