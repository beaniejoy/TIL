# Spring에서 사용되는 Validation

> Spring (Boot)에서 사용되는 Validation에 대한 내용 TIL

<br>

## :pushpin: gradle 설정

```gradle
implementation 'org.springframework.boot:spring-boot-starter-validation'
```
- Spring Boot 2.3 버전 부터 `spring-boot-starter-validation` 의존성을 명시해야 Valid 사용 가능

<br>

## :pushpin: Valid 사용

### Request Dto 부분

```kotlin
data class ValidRequestDto(
    @field:NotNull(message = "value는 필수 입력값입니다.")
    val value: String? = null,

    @field:NotNull(message = "createdAt은 필수 입력값입니다.")
    val createdAt: LocalDate? = null,

    @field:NotNull(message = "number는 필수 입력값입니다.")
    val number: Long? = null
)
```
- kotlin으로 Valid 관련 제약사항을 명시할 때 `@field:` 앞에 prefix로 붙여야 한다.
- kotlin에서 기존 방식대로 `@NotNull` 이렇게 사용하면 constructor에 Valid 어노테이션이 붙게됨
- `constructor에 붙으면 Valid가 제대로 동작하지 않음` < 찾아볼 것

### Controller 부분

```kotlin
@GetMapping("/get")
fun getParam(
    @Valid @ModelAttribute validRequestDto: ValidRequestDto
)
```
- 이렇게 BindingResult 파라미터 없이 사용했을 때 Valid 에러 발생하면 관련 error message와 함께 그대로 `400(Bad Request)` 에러를 반환하게 된다.

```kotlin
@GetMapping("/get")
fun getParam(
    @Valid @ModelAttribute validRequestDto: ValidRequestDto,
    bindingResult: BindingResult
)
```
- `BindingResult`를 따로 handler에서 잡을 수 있다.
- 그런데 여기서 `BindingResult`에 담겨진 에러 내용을 확인하지 않고 로직을 진행하면 Valid 로직을 수행하지 않고 requestDto에 아무것도 담기지 않은 상태로 진행된다.

```kotlin
@GetMapping("/get")
fun getParam(
  @Valid @ModelAttribute validRequestDto: ValidRequestDto
): ValidRequestDto {
  if (bindingResult.hasErrors()) {
    logger.error("errors : ${bindingResult.fieldErrors}")
    throw RuntimeException("errors ${bindingResult.fieldErrors}")
  }

  //...
}
```
- 위와 같은 방식으로 `BindingResult`에 담겨진 에러 존재 유무를 확인하고 에러를 따로 던져야 한다.
- 해당 부분은 `org.springframework.validation`을 사용할 때 해당
- `@Valid` 적용된 부분 바로 뒤에 `BindingResult`가 붙어야 한다.

<br>

## :pushpin: Controller단 내 여러 Validation 사용 방법

### @ModelAttribute

```kotlin
@GetMapping("/get")
fun getParam(
  @Valid @ModelAttribute validRequestDto: ValidRequestDto
)
```
- 기본적으로 `400` 에러 반환(`BindException`)
- `@ModelAttribute`에서 발생하는 Validation Exception은 `BindException`으로 떨어진다.
- `BindingResult`를 받아서 Exception Handling 할 수 있다.

### @RequestParam
```kotlin
@RestController
@Validated
class ValidationController {
  @GetMapping("/get")
  fun getParam(
      @NotNull(message = "필수값입니다.") @RequestParam("value") value: String?
  ): String? {
    //...
  }
}
```
- `@RequestParam`은 클래스 단계에서 `@Validated` 어노테이션 필요
- 기본적으로 path variable, request param에 대한 validation 에러는 `500`을 반환한다. (`ConstraintViolationException` 발생)
> Any error during path or request validation in Spring results by default in an HTTP 500 response
- `@ControllerAdvice`를 이용해 `500` > `400` 응답코드로 변환해 반환해줄 수 있음

### @PathVariable
```kotlin
@GetMapping("/{id}")
fun getParam(
    @PathVariable("id") @NotNull(message = "필수값입니다.") id: Long?
): Long? {
  //...
}
```
- 위의 `@RequestParam` 내용과 같다.

### @RequestBody
```kotlin
@PostMapping("/post")
fun postRequestBody(
    @Valid @RequestBody validRequestDto: ValidRequestDto
): ValidRequestDto {
  //...
}
```
- 기본적으로 `400` 에러코드 반환(`MethodArgumentNotValidException`)
- `@RequestBody`에 대한 validation 에러는 `MethodArgumentNotValidException`로 떨어지는데 `BindException`을 확장한 에러 클래스다.  
(그래서 `BindException`으로 Exception handling해도 잡힌다.)

### 

<br>

## :pushpin: Exception Handler

```kotlin
@ExceptionHandler(BindException::class)
fun handleNotValidException(
    e: BindException,
    bindingResult: BindingResult
): ResponseEntity<ErrorDto> {
    logger.error("validation errors : ${bindingResult.fieldErrors}")

    val defaultMessage = bindingResult.fieldError?.defaultMessage
    val code = bindingResult.fieldError?.code

    return ResponseEntity.badRequest()
        .body(
            ErrorDto(
                message = defaultMessage,
                validCode = code,
                description = e.message
            )
        )
}
```
- `BindException`, `MethodArgumentNotValidException`에 대한 exception handler에서 `BindingResult`를 직접 받아올 수 있다.