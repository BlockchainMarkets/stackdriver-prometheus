#!/usr/bin/env groovy

pipeline {
  agent any
  stages {
    stage("checkout") {
      steps {
        checkout scm
      }
    }

    def dockerImage
    stage("compile image") {
      steps {
        sh "make build-linux-amd64"
        dockerImage = docker.build('us.gcr.io/bmi-da-181915/stackdriver-prometheus', '.')
      }
    }

    state("push image") {
      steps {
        docker.withRegistry('https://us.gcr.io/', 'gcr:bmi-da-181915') {
            dockerImage.push 'latest'
        }
      }
    }
    stage('deploy to DA') {
      steps {
        build job: 'GKE deployment/Non-prod environment deployment', parameters: [string(name: 'GKE_CLUSTER', value: 'da'), string(name: 'GITHUB_PROJECT', value: env.JOB_NAME)], wait: false
      }
    }

    stage('clean up') {
      steps {
        cleanWs();
      }
    }
  }
}
