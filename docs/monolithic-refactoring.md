---
layout: default
title: Monolithic Refactoring
nav_order: 5
---

# Monolithic Refactoring
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## **마이크로서비스 리팩터링 개요**


### **모놀리스 서비스의 비즈니스 문제**
- 마이크로서비스로 리팩터링하는 작업은 새 기능 개발에 필요한 리소스가 분산되므로 중대한 비즈니스 문제를 마이크로서비스로 해결할 수 있을 때에만 마이크로서비스 도입을 추진해야 한다
- 느린 전달 slow delivery : 애플리케이션을 이해, 관리, 테스트하기 어려워서 개발자 생산성이 떨어지고, 경쟁사와 효율적으로 경쟁하기 어렵다
- 버그 투성이 소프트웨어 릴리스 buggy software release : 테스트하기 어려워서 소프트웨어 릴리스가 버그 투성이일 때가 많고 서비스에 불만을 품게 된 고객이 이탈한다
- 나쁜 확장성 : 리소스 요건이 전혀 다른 모듈을 하나의 컴포넌트로 조합하기 때문에 확장이 어렵고 현재의 또는 미래에 예상되는 고객의 니즈를 지원할 수 없다


### **스트랭글러 애플리케이션 패턴**
- 레거시 애플리케이션에 새로운 스트랭글러 애플리케이션을 에둘러 단계적으로 개발하는 애플리케이션 현대화 패턴
- 애플리케이션 현대화 application modernization : 레거시 애플리케이션을 현대 아키텍처와 최신 기술 스택이 접목된 애플리케이션으로 탈바꿈시키는 과정
- 기존 애플리케이션을 완전히 뜯어고치는 일은 리스크가 너무 크고 시간 낭비가 만만치 않으므로 모놀리식 애플리케이션을 단계적으로 리팩터링한다
- 기존 애플리케이션과 함께 실행되면서 새로운 마이크로서비스 애플리케이션을 조금씩 빌드한 스트랭글러 애플리케이션 strangler application 을 개발해서 단계적으로 모놀리식을 마이크로서비스로 전환
- 애플리케이션에서 가치가 큰 부분을 먼저 마이크로서비스로 이전하는 식으로 단계적인 리팩터링을 수행하면 투자에 따른 보상을 즉시 받을 수 있어 그 전환 가치를 경영진에게 일찍, 더 자주 보여 줄 수 있다
- 추출된 서비스에 있는 데이터를 모놀리스 DB에 도로 복제하거나 모놀리스에 미치는 영향을 줄이기 위해 서비스 추출 순서를 잘 조정하여 모놀리스를 리팩터링하는 작업량을 줄여야 한다
- 테스트 자동화 배포 파이프라인을 제외한 배포 인프라나 관측성 인프라 개발에 필요한 선행 투자는 최소화하는 것이 좋다


* * *


## **마이크로서비스 리팩터링 전략**


### **새로운 기능을 마이크로서비스로 구현**
- 구멍의 법칙 Law of Holes : 모놀리식 애플리케이션이 거의 관리 불능 상태라면 모놀리식 애플리케이션에 새 기능이 구현된 코드는 더 이상 추가하지 않는다
- 새로운 기능을 마이크로서비스로 구현하면 모놀리스는 성장을 멈추고 마이크로서비스의 가치가 신속하게 입증된다
- 새 서비스와 모놀리스 연계
  - API 게이트웨이 : 새 기능의 요청은 새 서비스로, 기존 요청은 모놀리스로 각각 라우팅
  - 통합 글루 코드 integration glue code : 새 서비스가 모놀리스 데이터에 접근하고 모놀리스에 구현된 기능을 호출할 수 있게 새 서비스를 모놀리스에 통합하는 코드
  - 통합 글루 코드는 스탠드얼론 컴포넌트가 아니라 모놀리스에 있는 어댑터 및 하나 이상의 IPC를 사용하는 서비스로 구성됨
- 새 기능을 서비스로 구현할지 여부 판단
  - 의미 있는 서비스라고 하기에는 기능 자체가 너무 작거나 새 기능이 기존 모놀리스 코드에 너무 단단히 매여 있는 경우에는 무리하게 서비스로 구현하면 과도한 IPC가 발생하여 성능 문제가 야기될 수 있으므로 일단 모놀리스에 새로운 기능을 구현
  - 나중에 관련 기능과 함께 해당 기능을 자체 서비스로 추출


### **표현 계층과 백엔드 분리**
- 표현 로직과 비즈니스 로직, 데이터 접근 로직은 보통 명확하게 구분되므로 표현 계층을 비즈니스 로직과 데이터 접근 계층에서 분리하여 표현 계층 애플리케이션이 비즈니스 계층 애플리케이션을 원격 호출하도록 한다
- 표현 계층 : HTTP 요청을 처리해서 웹 UI에 전달할 HTML 페이지를 생성하는 모듈로 구성. 사용자 인터페이스가 정교한 애플리케이션은 표현 계층이 코드 대부분을 차지
- 비즈니스 로직 : 복잡한 비즈니스 규칙이 구현된 모듈로 구성
- 데이터 접근 로직 : DB, 메시지 브로커 등 인프라 서비스에 접근하는 모듈로 구성
- 표현 계층과 백엔드를 서로 독립적으로 개발, 배포, 확장할 수 있고, 특히 표현 계층 개발자는 UI 작업을 신속히 반복하면서 A / B 테스트를 쉽게 수행할 수 있다
- 비즈니스 계층에 비즈니스 로직을 캡슐화한, 하나 이상의 퍼사드로 구성된 대단위 coarse-grained API가 추후 마이크로서비스가 호출할 수 있는 원격 API로 표출된다 
- 표현 계층과 백엔드 애플리케이션이 각각 여전히 관리하기 힘든 모놀리스가 될 소지가 있다


### **기존 기능을 마이크로서비스로 추출**
- 모놀리스가 가진 비즈니스 능력을 하나씩 서비스로 옮겨 점점 서비스로 구현한 능력이 많아지게 하고 모놀리스 규모를 축소한다
- 모놀리스에서 서비스로 추출해야 할 기능의 구성 요소
  - API endpoint 가 구현된 인바운드 어댑터
  - 도메인 로직
  - DB 접근 로직 등이 구현된 아웃바운드 어댑터
  - 모놀리스의 DB 스키마
- 마이크로서비스 추출 후 아키텍처
  - 모놀리스에서 코드를 추출해서 스탠드얼론 서비스로 이전
  - API 게이트웨이는 추출된 비즈니스 능력을 호출하는 요청은 해당 서비스로, 나머지 요청은 모놀리스로 각각 라우팅
  - 통합 글루 코드를 통해 모놀리스 및 서비스가 서로 협동
- 도메인 모델 분리 
  - 모놀리스 도메인 모델에서 서비스 도메인 모델을 추출하고 서비스 경계에 걸쳐 있는 (서비스 간 객체를 참조 하는) 객체 레퍼런스를 제거한다
  - DDD 애그리거트 관점으로 객체 레퍼런스 대신 기본키로 서로 참조하도록 대체한다
  - 객체 레퍼런스를 참조하는 클라이언트에 큰 영향을 미치는 경우 서비스와 모놀리스 간에 데이터를 복제하면 변경 범위를 줄일 수 있다
  - 클래스 내부에 깊숙이 내장된 기능을 추출하여 클래스를 분리해야 하는 경우도 있다
- DB 리팩터링
  - 도메인 모델의 클래스는 대부분 영속적이라서 필드가 DB 스키마에 매핑되어 있기 때문에 모놀리스에 있던 DB 테이블도 분리해서 서비스 DB로 옮겨야 한다
  - DB 스키마를 리팩터링하는 전략에 따라 데이터를 복제해서 단계적으로 업데이트하면 변경 범위를 줄일 수 있다
- 데이터 복제
  - 전이 기간 동안에는 원본 스키마를 유지하되, 원본 스키마와 신규 스키마를 동기화하는 트리거를 사용하는 방식으로 접근하면 값비싼 변경 작업을 나중으로 미루거나 우회할 수 있다
  - 전이 기간 중에는 원본 엔터티를 거의 고치지 않은 상태로 두고 새로 추출된 서비스 관련 필드는 읽기 전용으로 만든다
  - 서비스 데이터는 모놀리스에 복제해서 최신 상태를 유지하고 모놀리스에서 관련 필드를 업데이트하는 코드는 새로운 서비스를 호출하도록 변경한다 
  - 새로 추출된 서비스가 관련 데이터를 모놀리스 DB로 다시 복제하면 기존 엔터티 구조를 그대로 유지할 수 있어 모놀리스의 변경 범위를 최소화할 수 있다
- 추출할 서비스 선택
  - 비즈니스에 가장 중요하고 계속 발전하는 서비스를 가장 먼저 추출하는 것이 좋다
  - 모놀리스 개발을 동결하고 요건이 있을 때마다 서비스를 추출하면 모놀리스를 어쩔 수 없이 분해하게 만드는 장점은 있지만 단기 요건에 의해 서비스를 추출하게 되는 단점이 있다
  - 서비스 추출 시 기대되는 혜택을 애플리케이션 모듈별로 순위를 매겨 리팩터링 태스크를 애플리케이션 백로그 backlog 에 추가하면 좀 더 전략적이고 비즈니스 니즈와 긴밀히 맞물려 서비스를 추출할 수 있다
  - 개발 가속화 : 애플리케이션 개발 일정상 개발 분량이 많을 것으로 예상되는 파트를 서비스로 전환하면 개발 속도가 빨라진다
  - 성능, 확장성, 신뢰성 문제 해결 : 애플리케이션 성능, 확장성에 문제가 있거나 미덥지 못한 부분은 서비스로 전환할 가치가 충분하다
  - 다른 서비스를 추출할 수 있게 함 : 한 서비스를 추출하면 모듈 간 디펜던시 때문에 다른 서비스의 추출이 단순해지는 경우도 있다


* * *


## **서비스와 모놀리스 간 협동**

### **통합 글루 코드 설계**
- 새로운 기능을 서비스로 구현하거나 모놀리스에서 서비스를 추출하면 반드시 서비스와 모놀리스가 협동할 수 있게 해주는 통합 글루도 개발해야 한다
- 통합 글루는 특정 IPC를 이용하는 코드를 서비스 및 모놀리스 양쪽에 구성하며, 사용하는 IPC 종류마다 구조가 다르다
- 통합 글루 API 
  - 통합 글루가 어떤 IPC를 쓰는지 비즈니스 로직이 알 필요가 없으므로 IPC를 인터페이스로 캡슐화한 API를 정의하여 비즈니스 로직이 호출하도록 한다
  - 데이터 조회 서비스의 경우 Repository 인터페이스를 정의하고, 서비스 작업을 호출하는 경우 서비스 인터페이스를 정의한다
- 상호 작용 스타일과 IPC 선택
  - 조회 API를 호출하여 데이터를 조회 : 리포지터리 인터페이스를 구현한 어댑터로 데이터 프로바이더의 API를 호출하는 방식으로 단순하지만 요청 개수가 많아지면 효율이 나빠질 공산이 크고 가용성이 떨어진다
  - 데이터 컨슈머가 데이터 레플리카 (CQRS 뷰)를 유지하여 데이터를 조회 : 데이터 컨슈머는 데이터 프로바이더가 발행한 도메인 이벤트를 구독해서 레플리카를 항상 최신 상태로 유지하는 방식으로 데이터 프로바이더를 쿼리하는 오버헤드가 감소하고 쿼리 효율을 높일 수 있지만 레플리카를 관리해야 하는 복잡성이 불가피하다
  - 데이터 수정 : 서비스와 모놀리스의 데이터 일관성을 유지해야 하므로 트랜잭셔널 메시징으로 통신해야 하며, 단순한 경우 요청자가 알림 메시지를 보내거나 업데이트를 트리거하는 이벤트를 발행하면 되고, 복잡한 경우 요청자가 사가를 이용하여 데이터 일관성을 유지해야 한다
- 부패-방지 계층 Anti-Corruption Layer 패턴
  - 상이한 두 도메인 모델이 서로 상대편을 더럽히지 않도록 변환해 주는 소프트웨어 계층
  - 부패 방지 계층의 목표는 레거시 모놀리스의 도메인 모델이 서비스 도메인 모델을 더럽히지 못하게 만드는 것으로 서로 다른 두 도메인 모델 간의 변환을 담당하는 코드 계층을 의미한다
  - 모놀리스를 호출하는 서비스 어댑터는 모놀리스 도메인 모델로 반환되는 반환 값을 서비스 도메인 모델로 변환해주어야 한다
  - 모놀리스 도메인 이벤트를 구독하는 이벤트 핸들러도 이벤트 발행기의 도메인 모델을 구독기의 도메인 모델로 변환해야 한다
  - 모놀리스도 서비스를 호출하고 서비스가 발행한 도메인 이벤트를 구독하는 경우 ACL을 이용하여 도메인 모델을 변환해야 한다
- 모놀리스가 도메인 이벤트를 발행 / 구독하는 방법
  - 서비스가 사용하는 것과 동일한 이벤트 발행 장치를 적용 : 특정 엔터티를 변경하는 코드를 모조리 찾아내 이벤트 발행 API를 호출하는 코드를 끼워넣어야 하므로 바꾸기가 쉽지 않다
  - DB 수준에서 도메인 이벤트를 발행 : 트랜잭션 로그 테일링이나 폴링을 사용하는 방법으로 모놀리스를 변경할 필요가 없어서 간편하지만 고수준의 비즈니스 이벤트를 발행하기가 어려워 테이블 변경 수준의 이벤트만 발행된다
  - 서비스 도메인 이벤트 구독 : 이벤트 핸들러를 작성하거나 이벤트를 구독하는 작은 헬퍼 helper 애플리케이션을 작성해서 모놀리스 DB를 직접 업데이트한다

 
### **데이터 일관성 유지**
- 사가 핵심 용어
  - 사가 saga : 비동기 메시징을 통해 편성되는 일련의 로컬 트랜잭션
  - 보상 트랜잭션 compensating transaction : 로컬 트랜잭션이 업데이트한 내용을 언두하는 트랜잭션
  - 대책 countermeasure : 사가 간 비격리 문제를 해결하는 데 사용되는 대체 설계 기법
  - 시맨틱 락 semantic lock : 사가에 의해 업데이트되는 레코드에 플래그를 세팅하는 대책
  - 보상 가능 트랜잭션 compensatable transaction : 사가의 후속 트랜잭션 중 하나가 실패할 가능성이 있어서 보상 트랜잭션이 필요한 트랜잭션
  - 피봇 트랜잭션 pivot transaction : 사가의 진행 / 중단 지점에 위치한 트랜잭션. 피봇 트랜잭션이 성공하면 사가는 완료될 때까지 실행된다
  - 재시도 가능 트랜잭션 retriable transaction : 피봇 트랜잭션 다음에 나오는 트랜잭션으로 반드시 성공한다
- 보상 트랜잭션을 지원하도록 모놀리스를 고치려면 고쳐야 할 모놀리스 코드가 많아서 시간이 많이 걸리고 개발 리소스도 낭비되고 위험성이 높다 
- 서비스를 추출할 때 보상 트랜잭션을 구현해야 하는 서비스를 추출하여 모놀리스에 보상 트랜잭션을 구현할 필요가 없는 사가를 설계하면 모놀리스 변경 작업을 최소화할 수 있다
- 서비스 추출 순서를 잘 조정하면 모놀리스에 보상 트랜잭션을 구현할 필요가 없도록 여러 서비스를 단계적으로 추출할 수 있다 

 
### **인증 / 인가 처리**
- 인-메모리 세션 상태를 관리하고 스레드 로컬을 활용하여 사용자 신원을 전달하는 모놀리식 보안 메커니즘과 JWT 토큰 형태로 사용자 신원을 전달하는 마이크로서비스 보안 메커니즘을 동시에 지원해야 한다
- 모놀리스의 로그인 핸들러를 수정하여 세션 쿠키와 함께 사용자 정보가 포함된 JWT 토큰 형태의 USERINFO 쿠키를 반환하도록 하면, 모놀리스와 서비스의 보안 메커니즘을 동시에 지원할 수 있다
- 이벤트 순서
  - 클라이언트가 사용자 자격증명을 포함시켜 로그인 요청을 한다
  - API 게이트웨이는 로그인 요청을 모놀리스에 보낸다
  - 모놀리스는 JSESSIONID 세션 쿠키, 사용자 정보가 있는 USERINFO 쿠키가 포함된 응답을 반환한다
  - 클라이언트는 USERINFO 쿠키가 포함된 요청을 보내 서비스를 호출한다
  - API 게이트웨이는 USERINFO 쿠키를 검증 후 Authorization 요청 헤더에 넣어 서비스를 호출한다
  - 서비스는 USERINFO 쿠키 검증 후 사용자 정보를 추출한다
- 모놀리스 LoginHandler
  - 스프링 시큐리티나 패스포트 같은 보안 프레임워크를 이용하여 POST 전송된 사용자 자격증명을 처리해서 사용자를 인증하고 사용자 정보를 세션에 저장한다
  - 기본 인-메모리 세션을 설정한 경우 JSESSIONID 세션 쿠키가 세팅된 HTTP 응답이 반환되는데, 마이크로서비스로 전환하려면 USERINFO 쿠키도 함께 세팅하도록 변경한다
- API 게이트웨이
  - 요청을 라우팅하고 API를 조합하는 일을 담당하는 API 게이트웨이는 USERINFO 쿠키를 검증 후 이 쿠키를 Authorization 요청 헤더에 넣어 서비스에 전달한다
  - 모놀리스 로그인 핸들러만 고쳐도 서비스가 사용자 정보에 접근할 수 있으므로 비즈니스 측면에서 가장 가치가 큰 서비스 개발에 집중할 수 있고, 사용자 관리처럼 상대적으로 중요도가 떨어지는 서비스는 나중에 추출한다


* * *

 
**cf. 피처 토글 feature toggle**
{: .no_toc .text-gamma }
- 피처 플래그 feature flag 를 사용하여 코드 변경분을 사용자에게 릴리스하지 않고 배포하는 방법. 기존 코드와 새 코드를 동적으로 선택하여 적용 가능하다