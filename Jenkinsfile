pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
        url = 'https://registry.hub.docker.com'
        credentialsId = 'docker'
        
    }
    stages {
        stage("Git checkout ") {
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/RAM28EC/Petclinic.git'
            }
        }
        stage("Compile") {
            steps{
                sh "mvn clean compile"
            }
        }
        stage("Unit Test") {
            steps{
                sh "mvn test"
            }
        }
        stage("sonarqube analysis") {
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.host.url=http://13.233.253.161:9000 \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
    
                }
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("bUILD") {
            steps{
                sh "mvn clean install"
            }
        }
        
        
        
        stage("Docker build and push") {
            steps{
                withDockerRegistry(url, credentialsId){
                    sh "docker build -t pet-clinic186 . "
                    sh "docker tag pet-clinic186  28cloud/pet-clinic186:latest "
                    sh "docker push 28cloud/pet-clinic186:latest "
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh " trivy image 28cloud/pet-clinic186:latest"
            }
        }
        stage("Deploy Using Docker"){
            steps{
                sh " docker run -d --name pet111 -p 8082:8082 28cloud/pet-clinic186:latest:latest "
            }
        }

    }
    

}
