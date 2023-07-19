# Tutorials


## secret 관리

```shell
$ vault kv put secret/foo bar=baz
$ vault kv put secret/foo hello=world

$ vault kv get secret/foo
==== Data ====
Key      Value
---      -----
hello    world
```
put할 때 주의사항이 이전 내용을 덮어씌운다는 점이 있다.  

```shell
$ vault kv put secret/foo bar=baz hello=world
```
이런 식으로 이어서 붙여서 값을 넣어야한다.