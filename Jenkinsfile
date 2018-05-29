#!/usr/bin/env groovy

pipeline {
  agent any
  stages {
    stage("checkout") {
      steps {
        checkout scm
      }
    }

    stage("compile & build image") {
      steps {
        sh "make build-linux-amd64"
        script {
            def dockerImage = docker.build('us.gcr.io/bmi-da-181915/stackdriver-prometheus', '.')
        }
      }
    }

    stage("build & push docker image") {
      steps {
        script {
          docker.withRegistry('https://us.gcr.io/', 'gcr:bmi-da-181915') {
            dockerImage.push('latest')
          }
        }
      }
    }
    stage('deploy to DA') {
      steps {
        build job: 'GKE deployment/Non-prod ad-hoc pods deployment', parameters: [string(name: 'GKE_CLUSTER', value: 'da'), string(name: 'CONFIG', value: 'deployments/prometheus-service')], wait: false
      }
    }

    post {
      always {
        cleanWs();
      cleanWs();
      }
    }
  }
}
