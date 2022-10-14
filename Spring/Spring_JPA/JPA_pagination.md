# JPA Pagination

```java
Page<Product> allProducts = productRepository.findAll(firstPageWithTwoElements);

List<Product> allTenDollarProducts = 
  productRepository.findAllByPrice(10, secondPageWithFiveElements);
```
- 위 내용 처럼 findAll 조회에서 Pageable 객체를 파라미터로 가진다면 `Page<T>` Object를 default로 return
- JPA repository 내에 custom method를 통해 `List<T>`로 전환가능

## Default 설정
```java
public ResponseEntity<List<CafeResponseDto>> getCafeList(
        @PageableDefault(sort = "name", direction = Sort.Direction.ASC, size = 10) Pageable pageable) {
    List<CafeResponseDto> cafeResponseList = cafeService.getCafeList(pageable);

    return ResponseEntity.ok(cafeResponseList);
}
```
- `@PageableDefault` 어노테이션을 통해서 default page 정보 설정 가능











































































































