pipeline {
  environment {
    registry = "rajendrakumarm/docker"
    registryCredential = 'docker_hub_login'
    dockerImage = 'rajendrakumarm/docker'
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
                  sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@prod_ip \"docker pull rajendrakumarm/train-schedule:${env.BUILD_NUMBER}\""
                   try  { 
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@prod_ip \"docker stop train-schedule\""
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@prod_ip \"docker rm train-schedule\""
                    } catch (err) {
                        echo: 'caught error: $err'
                    }
                    sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@prod_ip \"docker run --restart always --name train-schedule -p 8080:8080 -d  rajendrakumarm/train-schedule:${env.BUILD_NUMBER}\""
                    }
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

