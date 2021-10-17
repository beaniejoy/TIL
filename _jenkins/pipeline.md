# Jenkins Pipeline 작성하기

## 목표
- Jenkins를 이용한 EC2 간단한 CI/CD pipeline 구성

## Jenkinsfile 작성

- Jenkins classic UI
  - Jenkins 내에서 직접 pipeline item 생성 후 설정에서 입력
- pipeline script from SCM
  - github repo 내의 Jenkinsfile 생성 후 미리 입력한 내용으로 build
- [Blue Ocean](https://www.jenkins.io/doc/book/blueocean/getting-started/)

### Declarative Pipeline
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
```

### Scripted Pipeline

```groovy
node {
    stage('Example') {
        if (env.BRANCH_NAME == 'master') {
            echo 'I only execute on the master branch'
        } else {
            echo 'I execute elsewhere'
        }
    }
}
```

## References

- [Jenkins Doc - Using a Jenkinsfile](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/)
- [Pipeline keywords](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#pipeline-nodes-and-processes)
