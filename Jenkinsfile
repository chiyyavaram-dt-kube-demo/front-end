@Library('dynatrace@master') _

pipeline {
  agent {
    label 'maven'
  }
  environment {
    APP_NAME = "front-end"
    ARTEFACT_ID = "kube-demo/" + "${env.APP_NAME}"
    VERSION = readFile('version').trim()
    DOCKER_REPO = "${env.DOCKER_REGISTRY_URL}/${env.APP_NAME}"
    DEV_TAG = "${env.VERSION}-${env.BUILD_NUMBER}"
    TAG = "${env.VERSION}"
  }
  stages {
    stage('Docker build') {
      steps {
        container('docker') {
          sh "docker build -t ${env.DOCKER_REPO} ."
          sh "docker tag ${env.DOCKER_REPO} ${env.DOCKER_REPO}:${env.TAG}"
          sh "docker tag ${env.DOCKER_REPO} ${env.DOCKER_REPO}:${env.DEV_TAG}"
        }
      }
    }
    stage('Docker push to registry'){
      steps {
        container('docker') {
          withCredentials([usernamePassword(credentialsId: 'registry-creds', passwordVariable: 'TOKEN', usernameVariable: 'USER')]) {
            sh "docker login --username=${USER} --password=${TOKEN} https://${env.DOCKER_REGISTRY_URL}"
            sh "docker push ${env.DOCKER_REPO}:${env.TAG}"
            sh "docker push ${env.DOCKER_REPO}:${env.DEV_TAG}"
          }
        }
      }
    }
  }
}
