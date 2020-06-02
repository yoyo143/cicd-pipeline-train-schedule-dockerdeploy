pipeline {
  environment {
    registry = "rajendrakumarm/docker"
    registryCredential = 'yoyo143'
    dockerImage = 'https://github.com/yoyo143/cicd-pipeline-train-schedule-dockerdeploy.git'
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/yoyo143/cicd-pipeline-train-schedule-dockerdeploy.git'
      }
    }
    stage('Build') {
       steps {
         sh 'npm install'
       }
    }
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}
