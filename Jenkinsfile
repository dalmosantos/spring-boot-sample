pipeline {
   agent {
      docker {
         image 'maven:3-alpine'
         label 'my-defined-label'
         args  '-v ${JENKINS_HOME}/.m2:/root/.m2'
      }
   }
   stages {
    stage('checkout project') {
      agent none
      steps {
        checkout scm
      }
    }
    stage('check docker install and build env') {
      agent none
      steps {
        sh '''
         make start-docker-registry'''
      }
    }
    stage('test project and serve') {

      steps {
        sh '''
        mvn test
        '''
      }
    }
    stage('wait for confirm alpha') {
      agent none
      parallel {
        stage('start server') {
          steps {
            sh "docker-compose up -d server"
          }
        }
        stage('wait for confirm alpha') {
          steps {
            input(message: 'Does staging at http://localhost:8000 look good?', ok: 'Deploy to production', submitter: 'admin', submitterParameter: 'PERSON')
            echo 'Hello, ${PERSON}, nice to meet you.'
          }
        }
        stage('junit report') {
          steps {
            junit '**/target/surefire-reports/TEST-*.xml'
          }
        }
        stage('Archive') {
          steps {
            archiveArtifacts '**/target/*.jar'
          }
        }
      }
    }
    stage('stop alpha') {
      agent none
      steps {
        sh 'docker-compose stop server'
      }
    }
    stage('package project') {
      steps {
        sh '''
         mvn package
         make build-docker-prod-image
         '''
      }
    }
    stage('deploy project') {
      agent none
      steps {
        sh '''
         docker push localhost:5000/java_sample_prod
         make deploy-production-local
         '''
      }
    }
    
  }
}