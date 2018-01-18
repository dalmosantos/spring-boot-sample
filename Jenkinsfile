pipeline {
  agent any
  stages {
    stage('checkout project') {
      steps {
        checkout scm
      }
    }
    stage('bulid evn') {
      steps {
        sh '''make start-docker-registry
make build-docker-env'''
      }
    }
    stage('test') {
      steps {
        sh '''docker-compose run --rm test
docker-compose up -d server'''
      }
    }
    stage('QA alpha') {
      steps {
        input(message: 'it is OK?', ok: 'go!', submitter: 'admin')
      }
    }
    stage('stop alpha') {
      steps {
        sh 'docker-compose stop server'
      }
    }
    stage('deploy') {
      steps {
        sh '''make build-docker-prod-image
docker push localhost:5000/java_sample_prod
make deploy-production-local'''
      }
    }
  }
}