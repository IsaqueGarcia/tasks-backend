pipeline{
    agent any
    stages{
        stage('Build backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests'){
            steps{
                bat 'mvn test'
            }
        }
        stage('Sonar Analysis'){
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=d15bbab62ead1dff7badd17e883970be26a5bec9 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                sleep(5)
                timeout(time:1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }                
            }
        }
        stage('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Test'){
            steps{
                dir('api-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/IsaqueGarcia/tasks-api-test'
                    bat 'mvn test'
                }
            }
        }
        stage('Deploy FrontEnd'){
            steps{
                dir('frontend'){
                    git credentialsId: 'github_login', url: 'https://github.com/IsaqueGarcia/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }           
            }
        }
        stage('Functional Test'){
            steps{
                dir('functional-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/IsaqueGarcia/tasks-functional-tests'
                    bat 'mvn test'
                }
            }
        }
    }
}


 
https://github.com/IsaqueGarcia/tasks-functional-tests

