# Monolithic, MSA, SOA

# Monolithic Architecture

모든 것이 하나의 프로젝트에 묶여있는 것

### 장점

- 하나의 애플리케이션만 개발하면 됨
- 컴포넌트간 함수 호출을 사용하기 때문에 높은 성능
- 개발 리포지터리 관리등 운영/관리가 용이함

### 단점

- 규모가 커지면 빌드/배포 시간, 서버 가동시간등이 오래걸림
- 하나의 실수가 전체 시스템에 영향을 줌
- 프로젝트 규모가 클 수록 협업이 어려움
- 개발자 개개인이 대략적인 전체 시스템을 이해해야 함
- 컴포넌트 별로 다른 기술을 도입하기 어려움

# Micro service Arichitecture

## MSA?

- 작은 서비스들의 결합을 통해 하나의 응용 프로그램을 개발하는 방법
- 각각의 서비스는 독립적인 비즈니스 로직으로 구성
- 충분히 작은 크기로 나누어 개발하되 상호 연계를 통해 좀 더 복잡하고 거대한 시스템을 만들어 갈 수 있다.
- 스케일링에도 높은 자유도를 가지고 있음

## 장점

- 하나의 서비스가 장애가 발생하더라도 나머지는 문제가 없음
- 서비스를 독립적으로 개발 및 배포/운영 (Loosly coupled)
- 독립적인 서비스별로 확장을 유연하게 할 수 있음
- 소프트웨어의 구조가 조직 구조와 일치 할 수 잇음
- 새로운 기술 도입 및 변경이 용이함
- 서비스의 재사용성이 높음

## 단점

- API를 통해 통신하기 때문에 성능이 떨어짐
- 서비스간의 트랜잭션을 묶을 수 없음
- 공통 기능에 대해서 서비스별로 중복이 발생
- 높은 기술적 이해와 개발 구성원의 높은 성숙도가 필요함

## MSA 를 도입하기 전에

### performance

네트워크 통신에 의한 비용이 추가로 발생하여 성능이 떨어질 수 있음

### Transaction Processing

트랜잭션 관리가 까다로움 

해결책

- 기획 자체에서 분산 트랜잭션 시나리오를 없애는 법
- 반드시 트랜잭션이 하나로 관리되어야 하는 부분에 대해서는 monolithic으로 접근
- 트랜잭션 실패시 처리하는 로직을 구현
- 복합 서비스를 만들어서 관리하는 방법

### Code Duplication

동일한 기능을 수행하는 코드가 서비스별로 중복작성 될 수 잇음

해결책

- API Gateway에서 처리할 수 있도록 설계
    - 각 서비스의 API들 앞에서 Endpoint를 통합하고, 공통 기능 등의 추가 기능을 제공하는 미들웨어
    - SOA의 ESB 경량화 버전
    - 무거운 로직을 수행하거나 잘못 설계되면 실패 가능성이 매우 높은 컴포넌트

### Data Duplication

서비스별로 데이터를 저장하며 중복이 생길 수 있음

적당한 데이터 모델링으로 디자인하여 해결해야 함

### Memory Duplication

동일한 모듈이 중복으로 올라가며 메모리가 필요보다 더 많이 사용될 수 있음

현대 인프라에서는 끄떡 없음

### Operation Overhead

서비스 개수가 늘어나며 많은 양의 배포 및 릴리즈 작업이 수반

각 서비스들이 다른 기술을 사용할 경우 학습해야 하는 기술도 늘어나게 됨

배포 및 릴리즈에 수반되는 모든 작업을 자동화해야 함

### Testing Chanllenge

런타임 환경에서의 상호작용 테스트가 까다로움

# SOA

## SOA?

- Service Oriented Architecture
- 서비스 단위로 개발하고, 개발된 서비스들을 공유함으로써 재가용성을 늘리고 유연성을 확보한다.
- 서비스에는 완전한 개별 비즈니스 기능을 수행하는 데 필요한 코드와 통합 데이터가 들어 있으며, 원격으로 서비스에 액세스할 수 있고 독립적으로 상호작용하거나 업데이트할 수 있습니다.

# SOA vs MSA

- SOA가 전사적인 아키텍처 접근 방식이라면 마이크로서비스는 애플리케이션 개발 팀 내의 구현 전략
- 서비스의 공유
    - SOA (최대한 서비스 공유) > MSA (최대한 독립적)
- 서비스의 상대적인 크기
    - SOA (비즈니스 중심) >  MSA (작은 서비스 하나)
- 기술구조
    - SOA: EBS라는 공통된 채널을 통해 서비스를 제공
    - MSA: 개별적인 API로 서비스를 제공
- 

## 참고

- [https://medium.com/@goinhacker/microservices-a9d773c159cc](https://medium.com/@goinhacker/microservices-a9d773c159cc)
- [https://www.redhat.com/ko/topics/cloud-native-apps/what-is-service-oriented-architecture](https://www.redhat.com/ko/topics/cloud-native-apps/what-is-service-oriented-architecture)
- [https://always-kimkim.tistory.com/entry/SOA와-MSA의-차이점](https://always-kimkim.tistory.com/entry/SOA%EC%99%80-MSA%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)
- [https://hsboee.medium.com/msa-vs-soa-e933ef582f3b](https://hsboee.medium.com/msa-vs-soa-e933ef582f3b)
