## Spring Data JPA에서 새로운 Entity를 판단하는 방법?
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
