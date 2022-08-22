# docker compose 활용한 애플리케이션 통합 실행

- docker compose 구성시 실제 실행 순서를 중요시 해야 할 듯
- 생각해보면 클라우드 배포시 애플리케이션 따로 db 따로 nginx 따로 존재하게 되는데 docker compose가 의미가 있을지 의문

<br>

## db, application 실행 순서 이슈
- db, app을 하나의 docker compose로 구성해서 `depends_on`으로 build 순서를 정했다 해도 실행순서를 보장받지 못함
- application이 먼저 실행되고 db가 이후에 실행되어 docker compose build 실패를 초래하는 이슈 발생
- 여러가지 해결책 존재
  - dockerize를 활용한 순서 보장 (wait 기법)
  - `wait-for-it` 을 활용할 수 있나 찾아봐야 될 듯 ([github](https://github.com/vishnubob/wait-for-it))

```dockerfile
FROM openjdk:17-alpine

ENV DOCKERIZE_VERSION v0.6.1
ENV WORK_DIR=/usr/app/

WORKDIR $WORK_DIR

COPY --from=BUILD_IMAGE $WORK_DIR/build/libs/*.jar dongne-api.jar

# run in order through dockerize utility (alpine version)
# link - https://github.com/jwilder/dockerize
RUN apk add --no-cache openssl bash

RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

COPY ./script/docker-entrypoint.sh docker-entrypoint.sh

RUN chmod +x docker-entrypoint.sh

ENTRYPOINT ["./docker-entrypoint.sh"]
```
- `RUN apk add --no-cache openssl bash`
  - dockerize github에는 wget을 위한 openssl 까지만 설치하는 내용이 나옴
  - bash는 이후 shell script 실행을 위해 필요
    - `Standard_init_linux.go 228: exec user process caused operation not permitted` 에러 발생 이슈
    - alpine 환경에서는 bash가 기본적으로 설치되어 있지 않음