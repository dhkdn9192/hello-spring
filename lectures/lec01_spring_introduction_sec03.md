## 섹션3. 회원 관리 예제 - 백엔드 개발

### 3-1. 비즈니스 요구사항 정리

```
  [컨트롤러] ---> [서비스] ---> [리포지터리] ---> [DB]
      |           |             |
      |           V             |
       -------> [도메인] <--------
```
* 일반적인 웹 애플리케이션 계층 구조
  * 컨트롤러 : 웹 MVC의 컨트롤러 역할
  * 서비스 : 핵심 비즈니스 로직 구현
  * 리포지터리 : DB에 접근, 도메인 객체를 DB에 저장하고 관리
  * 도메인 : 비즈니스 도메인 객체 (예: 회원, 주문, 쿠폰 등 주로 DB에 저장하고 관리됨)

