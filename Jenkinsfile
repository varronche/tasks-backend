pipeline{
    agent any
    stages {
        stage('Build Backend') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL') {
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=8536dc3b9c724062803ef90e36e0d11c339bb258 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**"
                }
            }
        }
        stage('Quality Gate') {
            steps {
                sleep(15)
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Test') {
            steps {
                git credentialsId: 'github_login', url: 'https://github.com/varronche/tasks-api-test.git'
            }
        }
    }
}





