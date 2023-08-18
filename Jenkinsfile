pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
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
        stage("Test") {
            steps{
                sh "mvn test"
            }
        }
        stage("sonarqube analysis") {
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
    
                }
            }
        }
        stage("bUILD") {
            steps{
                sh "mvn clean install"
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ' , odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Docker build and push") {
            steps{
                withDockerRegistry(credentialsId: 'docker'){
                    sh "docker build -t pet-clinic186 ."
                    sh "docker tag pet-clinic186  28cloud/pet-clinic186:latest"
                    sh "docker push 28cloud/pet-clinic186:latest"
                }
            }
        }
        
    }
    

}
