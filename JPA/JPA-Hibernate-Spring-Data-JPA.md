## JPA, Hibernate, Spring Data JPA의 차이?
* **JPA**는 **기술 명세**이다.
    * 자바 애플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 인터페이스
    * JPA는 단순한 명세이기 때문에 인터페이스와 규약만 정의하며, 실제 구현체는 제공하지 않는다.
* **Hibernate**는 **JPA의 구현체** 중 하나이다.
    * JPA가 정의한 `javax.persistence.EntityManager` 같은 인터페이스를 직접 구현한 라이브러리
* **Spring Data JPA**는 **JPA를 쉽게 사용하기 위해 해주는 모듈**이다.
    * JPA를 추상화시킨 Repository 인터페이스를 제공
        * Repository의 기본 구현체인 `SimpleJpaRepository`는 내부적으로 `EntityManager`를 사용한다.
