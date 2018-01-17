pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v $HOME/.m2:/root/.m2 -p 8000:8000'
        }
    }
    stages {
        stage('checkout') {
            steps {
                checkout scm
            }
        }
        stage('test') {

            steps {
                sh 'mvn test package'
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts '**/target/*.jar'
            }
        }
    }
}