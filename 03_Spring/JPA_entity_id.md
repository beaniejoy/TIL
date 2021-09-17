# JPA ID Generate

## AUTO_INCREMENT

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long reviewCountId;
```

```sql
`review_count_id` int NOT NULL AUTO_INCREMENT,
```

## UUID

```java
@Id
@GeneratedValue(generator = "uuid2")
@GenericGenerator(name = "uuid2", strategy = "uuid2")
@Column(name = "history_id", columnDefinition = "BINARY(16)")
private UUID historyId;
```