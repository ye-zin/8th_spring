# 핵심 키워드
## **지연로딩과 즉시로딩의 차이**
### Fetch Type
    JPA가 하나의 Entity를 조회할 때, 연관 관계에 있는 객체들을 어떻게 가져올 것인지 나타내는 설정 값.
    JPA는 ORM 기술로, 사용자가 직접 쿼리를 생성하지 않고, JPA에서 JPQL을 이용하여 쿼리문을 생성하기 때문에 객체와 필드를 보고 쿼리를 생성.
    따라서, 다른 객체와 연관관계 매핑이 되어있으면 그 객체들까지 조회하게 되는데, 이때 이 객체를 어떻게 불러올 것인 설정할 수 있음.
    fetch의 디폴트 값을 @xxToOne에서는 EAGER, @xxToMany에서는 LAZY.

### 즉시로딩(Eager)
    데이터를 조회할 때, 연관된 모든 객체의 데이터까지 한번에 불러오는 것.
    즉시 로딩 방식을 사용하면 Member 조회하는 시점에 바로 Team까지 불러오는 쿼리를 날려 한꺼번에 데이터를 불러올 수 있음.
    @xxToxx(fetch = fetchType.EAGER)

### 지연로딩(Lazy)
    필요한 시점에 연관된 객체의 데이터를 불러오는 것.
    지연 로딩 방식을 사용하면 Member를 조회하는 시점이 아닌 실제 Team을 사용하는 시점에 쿼리가 나가도록 할 수 있다는 장점이 있음.
    @xxToxx(fetch = fetchType.LAZY)

### 즉시로딩 vs 지연로딩
    Q. Member 데이터가 필요한 곳에 대부분 Team의 데이터 역시 같이 사용할 필요가 있다면?
    A. Fetch Type을 EAGER로 설정하여 항상 Member와 Team을 같이 조회해 오는 것이 더 좋을 것 → 즉시로딩

    Q. Member를 사용하는 곳 대부분에서 Team 데이터가 필요하지 않다면?
    A. Fetch Type을 LAZY로 설정하여 Member만 조회하고 Team이 필요할 땐 그때 Team에 대한 쿼리를 한번 더 날려 조회하는 것이 좋을 것

    → 가급적 지연 로딩을 사용하는 것을 권장 = 즉시 로딩은 JPQL에서 **N+1 문제**가 발생할 수 있기 때문

## **Fetch Join**
    일단, SQL에서 이야기하는 Join의 종류는 아님. JPQL에서 성능 최적화를 위해 제공하는 Join의 종류. 
    연관된 엔티티나 컬렉션을 한 번에 같이 조회할 수 있는 기능. JOIN FETCH 명령어로 사용.
    처음 조회부터  Join시켜 데이터를 가져올 수 있게 하려고 Fetch Join이 등장.

### 장점
    N+1 문제 해결: 지연 로딩으로 인해 발생하는 반복 쿼리를 줄임.
    성능 개선: 한 번의 쿼리로 연관된 데이터를 미리 가져올 수 있음.

### 특징
    `LAZY`로 설정하더라도 페치 조인을 사용하면 데이터가 즉시 조회되는 결과를 가져옴.
    -> 최적화가 필요하면 페치 조인을 적용하는 것이 효과적.
    객체 그래프를 유지할 때 사용하면 효과적.

### 한계
    Fetch Join 대상에는 별칭을 줄 수 없기에 SELECT, WHERE, 서브 쿼리에 Fetch Join 대상을 사용할 수 없음. 
    Why? 잘못된 별칭 사용으로 인해 데이터 무결성이 깨질 수 있으므로.

## @EntityGraph
    JPA에서 Fetch Join의 대안으로 사용되는 기능. → JPQL을 수정하지 않고도 연관된 엔티티를 즉시 로딩(Eager Loading)할 수 있게
    = 지연 로딩(Lazy Loading) 설정을 무시하고, 특정 연관 필드만 선택적으로 즉시 로딩할 수 있는 방법
    주로 N+1 문제 해결을 위해 사용. Fetch Join과 달리 페이징과 함께 사용할 수 있음.
    왜 사용 하는가?
    JPQL에서 JOIN FETCH를 계속 쓰다 보면 복잡해지고 유지보수가 어려워짐.
    JOIN FETCH는 컬렉션에 사용 시 페이징 불가.
    이를 대체하기 위해 선택적으로 연관 필드만 즉시 로딩할 수 있는 @EntityGraph를 활용.

## JPQL
    '객체‘를 대상으로 작성하는 객체 지향 쿼리 언어. 
    SQL이 DB에 있는 테이블을 조회하는 쿼리라고 한다면 JPQL은 엔티티 객체를 조회하는 객체지향 쿼리를 의미. 
    문법은 SQL과 비슷하고 SQL이 제공하는 기능을 유사하게 지원. 
    SQL처럼 데이터베이스와 상호 작용한면서, 객체 지향적인 특성을 반영한 쿼리를 날릴 수 있게 도와주는 것.

    JPQL은 SQL을 추상화 하였기에 특정 데이터베이스에 의존하지 않고 데이터베이스 방언만 변경하면 JPQL을 수정하지 않고 자연스럽게 데이터베이스를 변경할 수 있음. 
    또한 JPQL은 엔티티 직접 조회, 묵시적 조인, 다형성 지원 등의 기능을 제공하기에 SQL보다 간결하다는 장점을 가지고 있음.

## QueryDSL
    타입 안전하고 가독성이 높은 방식으로 SQL과 유사한 쿼리를 작성할 수 있게 해주는 프레임워크. 
    PQL, SQL보다 훨씬 직관적이고 안정적인 쿼리 작성을 가능하게 해줌. 

