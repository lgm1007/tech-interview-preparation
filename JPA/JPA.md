## 1. JPA, Hibernate, Spring Data JPA의 차이?
* **JPA**는 **기술 명세**이다.
  * 자바 애플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 인터페이스
  * JPA는 단순한 명세이기 때문에 인터페이스와 규약만 정의하며, 실제 구현체는 제공하지 않는다.
* **Hibernate**는 **JPA의 구현체** 중 하나이다.
  * JPA가 정의한 `javax.persistence.EntityManager` 같은 인터페이스를 직접 구현한 라이브러리
* **Spring Data JPA**는 **JPA를 쉽게 사용하기 위해 해주는 모듈**이다.
  * JPA를 추상화시킨 Repository 인터페이스를 제공
    * Repository의 기본 구현체인 `SimpleJpaRepository`는 내부적으로 `EntityManager`를 사용한다.

## 2. Spring Data JPA에서 새로운 Entity를 판단하는 방법?
* JpaEntityInformation의 `isNew()` 메서드로 판단한다.
  * `@Version`이 사용된 필드가 없거나 `@Version`이 사용된 필드가 primitive 타입이면 AbstractEntityInformation 클래스의 `isNew()`를 호출하는데, 이는 `@Id` 어노테이션을 사용한 필드를 확인해서 primitive 타입이 아니라면 null 여부, Number 타입이면 0인지 확인해서 참이면 새로운 Entity로 판단한다.
  ```java
   public boolean isNew(T entity) {

     Id id = getId(entity);
     Class<ID> idType = getIdType();

     if (!idType.isPrimitive()) {
         return id == null;
     }

     if (id instanceof Number) {
         return ((Number) id).longValue() == 0L;
     }

     throw new IllegalArgumentException(String.format("Unsupported primitive id type %s", idType));
  }
  ```
    * `@Version`이 사용된 필드가 Wrapper class이면 null 여부를 확인한다.
    * 만약 `@GeneratedValue` 어노테이션으로 키 생성 전략을 사용하지 않고 직접 ID를 할당하는 경우 새로운 Entity로 간주되지 않는다. 이 때는 `Persistable<T>` 인터페이스를 구현해서 JpaPersistableEntityInformation의 `isNew()`가 동작하도록 해야 한다.
