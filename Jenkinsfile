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
  }
}