#!/usr/bin/env groovy

pipeline {
  agent any
  stages {
    stage("checkout") {
      steps {
        checkout scm
      }
    }

    stage("compile") {
      steps {
        sh "make build-linux-amd64"
      }
    }

    stage("build & push docker image") {
      steps {
        docker.withRegistry('https://us.gcr.io/', 'gcr:bmi-da-181915') {
          docker.build('us.gcr.io/bmi-da-181915/stackdriver-prometheus', '.').push('latest')
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
