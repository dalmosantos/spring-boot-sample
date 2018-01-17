pipeline {
  agent any
  stages {
    stage('checkout project') {
      steps {
        checkout scm
      }
    }
    stage('check docker install and build env') {
      steps {
        sh '''docker -v
docker-compose -v
docker ps
make start-docker-registry
make build-docker-env'''
      }
    }
    stage('test project and serve') {
      steps {
        sh '''docker-compose run --rm test
docker-compose up -d server'''
      }
    }
    stage('report and archive') {
      steps {
        junit '**/target/surefire-reports/TEST-*.xml'
        archiveArtifacts '**/target/*.jar'
      }
    }
    stage('wait for confirm alpha') {
      steps {
        input(message: 'Does staging at http://localhost:8000 look good?', ok: 'Deploy to production', submitter: 'admin', submitterParameter: 'PERSON')
        echo 'Hello, ${PERSON}, nice to meet you.'
      }
    }
    stage('stop alpha') {
      steps {
        sh 'docker-compose stop server'
      }
    }
  }
}