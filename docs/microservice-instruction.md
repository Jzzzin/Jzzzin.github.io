---
layout: default
title: Micro Service Instruction
nav_order: 4
---

# Micro Service Instruction
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## **Architect 관점 : Micro Service Architecture 설계 시**

### **Business 문제의 분해에 관한 지침**
- Business 문제를 기술하고, Business 문제를 기술하는 데 사용되는 명사에 주목하라
- 핵심 동사는 행위를 부각하고 문제 영역의 윤곽을 드러낸다
- Micro Service는 Data 를 완전히 소유해야 한다

### **Service 세분화의 확정에 관한 지침**
- 큰 Micro Service 에서 시작해 작게 Refactoring하는 것이 더 낫다
- Service 간 교류하는 방식에 먼저 집중한다
- Service 책임은 계속 변한다 ( 진화론적 사고 과정으로 개발해야 한다 )


**cf. 나쁜 Micro Service 의 징후 - 너무 큰 경우**
{: .no_toc .text-gamma }
- 책임이 너무 많다
- 많은 Table 의 Data 를 관리한다 ( 3 ~ 5 개 이내의 테이블을 소유하는 것이 바람직 )
- Test Case 가 과다하다


**cf2. 나쁜 Micro Service 의 징후 - 너무 작은 경우**
{: .no_toc .text-gamma }
- 한 문제 영역에 속한 Micro Service 가 너무 많다
- Micro Service 가 지나치게 상호 의존적이다
- Micro Service 가 단순한 CRUD 집합이 된다


### **Service Interface 의 정의에 관한 지침**
- REST 철학을 수용하라 ( HTTP 동사를 기반으로 기본 행동 양식을 모델링 )
- URI 를 사용해 의도를 전달하라
- Request 와 Response 에 JSON 을 사용하라
- HTTP 상태 코드로 결과를 전달하라


* * *


## **Developer 관점 : Micro Service 개발 시**


### **REST Service 의 특징**
- Service 호출 Protocol 로 HTTP 를 사용한다
- Service 행동 양식을 HTTP 표준 동사에 매핑한다
- Data 직렬화 형식으로 JSON 을 사용한다
- HTTP 상태 코드를 사용해 Service 호출 상태를 전달한다


### **JSON 의 특징**
- 적은 텍스트로 Data 표현이 가능하다
- 가독성이 높고 사용하기 쉽다
- 자바스크립트에서 사용되는 기본 직렬화 Protocol 이다


### **Endpoint 표준에 관한 지침**
- Service 가 제공하는 Resource 를 알 수 있는 명확한 URL 이름을 사용하라
- Resource 간 관계를 알 수 있는 URL 을 사용하라
- URL 버전 체계를 일찍 세워라


* * *


## **DevOps 관점 : Micro Service 배포와 관리 시**


### **Service Assembly 단계**
- Micro Service 는 단일 산출물로 Packaging 하고 설치될 수 있어야 한다. 
- Micro Service 는 자체 완비형이며 독립적으로 배포 가능해야 한다
- Service Packaging 과 배포 방식은 반복성과 일관성을 보장해야 한다


### **Service Bootstrapping 단계**
- Micro Service 는 사람의 개입 없이 구성가능 해야 한다
- 구성 정보는 Service 의 환경 변수로 전달되거나 구성 저장소에서 읽어와야 한다
- 구성 저장소는 읽기 지연 시간이 짧아야 하고 완전히 중단되서는 안된다
- 구성 정보가 변경되면 이전 구성으로 실행되는 서비스가 제거되거나 구성 정보를 다시 읽어오도록 알려줘야 한다


### **Service Registration 및 Discovery 단계**
- Micro Service Instance 는 Client 가 위치를 알지 못하도록 투명해야 한다
- Micro Service Instance 는 시작 할 때 Service Discovery Agent 에 등록 한다
- Service Client 는 Service Discovery Agent 와 통신해 서비스 위치를 찾는다


### **Service Monitoring 단계**
- Micro Service 는 Service Discovery Agent 에 자신의 상태를 전달해야 한다
- Service Discovery Agent 는 등록된 Service 상태를 모니터링 한다
- Service Discovery Agent 는 잘못된 Service 를 제거하고 회피하여 라우팅해야 한다


* * *


## **Micro Service Application 구축 지침**
- Codebase : 모든 Application 코드와 서버 provisioning 정보는 버전관리 되어야 한다. 각 Micro Service 는 독립적인 코드 저장소를 가져야 한다.
- Dependencies : Application 이 사용하는 의존성은 빌드 도구를 이용해 명시적으로 선언해야 한다. 3rd Party JAR 의존성은 버전 번호를 붙여 명시해 선언해야 한다.
- Config : Application 환경별 구성을 코드와 독립적으로 저장해야 한다. 
- Backend Services : Database 나 Messaging Service 는 언제든 교체 가능해야 한다.
- Build, Release, Run : 빌드된 서비스는 불변적이므로 변경할 수 없다.
- Processes : Micro Service 는 무상태Stateless 방식을 사용해야 한다. 언제든 서비스를 강제 종료하거나 교체할 수 있다.
- Port Binding : Service 는 명령행에서 단독으로 시작하고 노출한 HTTP 포트를 통해 즉시 액세스 할 수 있어야 한다.
- Concurrency : 수직 확장 대신 수평 확장을 사용해야 한다.
- Disposability : Micro Service 는 요구에 따라 시작 및 중지 할 수 있다. 강제 종료 신호를 받으면 적절히 종료해야 한다.
- Dev / Prod parity : Service 가 실행되는 모든 환경 사이의 차이를 최소화하라. 이는 환경 간 서비스 배포가 수 시간안에 이루어져야 한다는 것을 의미한다.
- Logs : Micro Service는 로깅 동작 방식에 신경쓰지 않아야 하며, 개발자는 출력된 로그를 시각적으로 확인할 수 있어야 한다.
- Admin Processes : 관리 작업은 소스 코드 저장소에 유지 및 관리되는 스크립트에 의해 수행되어야 한다. 


* * *


## **Micro Service 를 사용하지 않는 경우**
- 분산 시스템 구축의 복잡성 : 고도로 분산된 Application 을 성공시키는 데 필요한 자동화와 운영 작업(모니터링)에 투자할 의사가 없는 경우
- 가상 서버/컨테이너의 Sprawl : 활용도가 낮은 여러 서버가 실제 작업량 보다 더 많은 공간과 리소스를 차지 하는 경우
- Application 유형 : 부서 수준의 소형 Application 이나 소수 사용자를 위한 Application 을 개발하는 경우
- Data 변환과 일관성 : Application 이 여러 Data Source 에서 복잡한 Data 를 취합하고 변환해야 할 경우