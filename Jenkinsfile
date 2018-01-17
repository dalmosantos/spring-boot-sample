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
    stage('wait for confirm alpha') {
      parallel {
        stage('wait for confirm alpha') {
          steps {
            input(message: 'Does staging at http://localhost:8000 look good?', ok: 'Deploy to production', submitter: 'admin', submitterParameter: 'string(name: \'PERSON\', defaultValue: \'Mr Jenkins\', description: \'Who should I say hello to?\')')
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
      steps {
        sh 'docker-compose stop server'
      }
    }
    stage('deploy project') {
      steps {
        sh '''docker-compose run --rm package
make build-docker-prod-image
docker push localhost:5000/java_sample_prod
make deploy-production-local'''
      }
    }
  }
}