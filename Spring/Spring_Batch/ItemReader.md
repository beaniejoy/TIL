# ItemReader 종류

## FlatFileItemReader

- 파일에 저장된 데이터를 읽어서 객체 매핑

```java
@Bean
public Step csvFileStep() throws Exception {
    return stepBuilderFactory.get("csvFileStep")
            .<Person, Person>chunk(10)
            .reader(csvFileItemReader())
            .writer(itemWriter())
            .build();
}

private FlatFileItemReader<Person> csvFileItemReader() throws Exception {
    DefaultLineMapper<Person> lineMapper = new DefaultLineMapper<>();
    DelimitedLineTokenizer tokenizer = new DelimitedLineTokenizer();
    tokenizer.setNames("id", "name", "age", "address");
    lineMapper.setLineTokenizer(tokenizer);

    // csv value 내용 추출 > jdbc resultSet 에서 뽑는것과 비슷
    lineMapper.setFieldSetMapper(fieldSet -> {
        int id = fieldSet.readInt("id");
        String name = fieldSet.readString("name");
        int age = fieldSet.readInt("age");
        String address = fieldSet.readString("address");

        return new Person(id, name, age, address);
    });

    FlatFileItemReader<Person> itemReader = new FlatFileItemReaderBuilder<Person>()
            .name("csvFileItemReader")
            .encoding("UTF-8")
            .resource(new ClassPathResource("test.csv")) // ClassPathResource: Spring에서 제공하는 resources 파일 앍울 수 있음
            .linesToSkip(1) // 첫번째 row (title)는 skip
            .lineMapper(lineMapper) // 한 줄씩 가면서 mapping 하는 클래스 설정
            .build();

    itemReader.afterPropertiesSet(); // itemReader 의 필수 설정값이 제대로 되어있는지 check

    return itemReader;
}
```
- `FlatFileItemReader`를 생성할 때 여러 설정 요소들이 필요
- `lineMapper`
  - 한 줄씩 읽어가면서 mapping해서 객체화하는 Mapper 설정
  - `DefaultLineMapper` Generic을 통해 lineMapper 구현체 생성
  - `DelimitedLineTokenizer` 설정
    - 일종의 column(칼럼 타이틀) 지정을 위해 tokenizer를 설정함
    - Tokenizer에 해당 칼럼 타이틀을 `String...`로 나열해서 지정해주면 된다.
    - `tokenizer.setNames("id", "name", "age", "address");`
  - `FieldSetMapper` 설정
    - 실제 row 데이터를 받아와서 객체화하기 위한 Mapper 설정
    - 람다를 이용해 `FieldSet` 인자를 받아서 각 column에 해당하는 데이터를 변수에 할당
    - 할당받은 변수를 원하는 형식의 클래스 객체를 만들어 return (JDBC `ResultSet`과 유사)
- `FlatFileItemReader`에 `name`, `encoding`, `resource` 설정
- `linesToSkip`: 몇번째 row까지 skip하고 나서 파일을 읽을 것인지 설정
- `afterPropertiesSet`: lineMapper가 제대로 설정됐는지 `Assert notNull` 확인