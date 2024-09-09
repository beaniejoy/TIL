# 영속성 관리 관련 정리

## 쓰기 지연(write behind)

### 벌크 연산

- `deleteAllByIdInBatch`
```java
public static final String DELETE_ALL_QUERY_BY_ID_STRING 
= "delete from %s x where %s in :ids";
```
in query를 이용한 bulk delete 기능  
`deleteAllByIdInBatch` 내부 구현코드를 보면 `executeUpdate()`를 통해 쿼리 실행하게 된다.  
중요한 특징은 이러한 벌크 연산은 영속성 컨텍스트를 무시하고 DB에 직접 쿼리  
> 즉, 쓰기지연 기능도 사용할 수 없고, 영속성 컨텍스트에도 변경된 내용들이 반영이 안되기 때문에 주의해서 사용해야 한다.

- 벌크 연산과 관련된 에러 상황(이미 삭제된 데이터에 대한 삭제 호출)

```kotlin
// CafeMenu
@OneToMany(mappedBy = "cafeMenu", fetch = FetchType.LAZY, cascade = [CascadeType.ALL])
val menuOptions: MutableList<MenuOption> = arrayListOf()
```
`CafeMenu` Entity에 위와 같이 처음에 MenuOption list에 대해 `CascadeType`을 `ALL`로 지정  
이유는 CafeMenu 도메인 안에 MenuOption, OptionDetail 모두 포괄하고 있고 CafeMenu의 생성과 수정, 삭제 모든 기능들에 같이 영향을 받을 수 밖에 없다라고 생각  

```kotlin
// CafeSeriesProcessorImpl
val cafeMenus = cafeSeriesReaderPort.getCafeMenus(menuIds)

cafeMenus.forEach { cafeMenu ->
    logger.info { "cafeMenu ${cafeMenu.id}" }
    // 1. bulk delete OptionDetails (immediately run query)
    cafeSeriesRemoverPort.bulkDeleteOptionDetails(
        cafeMenu.menuOptions.flatMap { menuOption ->
            menuOption.optionDetails.map { it.id }
        })

    // 2. bulk delete MenuOptions (immediately run query)
    cafeSeriesRemoverPort.bulkDeleteMenuOptions(
        menuOptionIds = cafeMenu.menuOptions.map { it.id }
    )
}

// 3. bulk delete CafeMenus (after complete bulk deleting MenuOptions, OptionDetails)
cafeSeriesRemoverPort.bulkDeleteCafeMenus(cafeMenus)
```
위에는 CafeMenu 삭제에 대해서 그 하위 도메인 모두를 삭제하는 기능이다.  
여기서 `MenuOption`, `OptionDetail` 둘 다 bulk 연산으로 한 번 쿼리로 삭제 진행  
벌크 연산 특성상 영속성 컨텍스트 거치지 않고 바로 DB에 쿼리 실행  
이 상태에서 

```text
Batch update returned unexpected row count from update [0]; actual row count: 0; expected: 1;  
statement executed: delete from option_details where option_detail_id=?;  
nested exception is org.hibernate.StaleStateException: Batch update  
returned unexpected row count from update [0];   
actual row count: 0; expected: 1;  
statement executed: delete from option_details where option_detail_id=?
```