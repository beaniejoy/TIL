# Pipeline 작성하기


## :pushpin: build 후 deploy까지 진행
```
pipeline {
    agent any
    tools {
        maven 'Maven3.8.6'
    }
    stages {
        stage('github clone') {
            steps {
                git branch: 'main', url: 'https://github.com/beaniejoy/cicd-web-project.git' 
            }
        }
        
        stage('build') {
            steps {
                sh '''
                    echo build start
                    mvn clean compile package -DskipTests=true
                '''
            }
        }
        
        stage('deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'deployer_user', path: '', url: 'http://10.163.185.55:8088')], contextPath: null, war: '**/*.war'
            }
        }
    }
}
```