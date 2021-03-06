pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Images') {
            when {
                branch 'master'
            }
            steps {
                script { 
                    app = docker.build("rajendrakumarm/train-schedule")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            } 
     }
        stage('Push Docker Images') {
            when {
                branch 'master'
            }
            steps {
                script { 
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        
        stage('DeploytoProduction') {
            when {
                branch 'master'
            }
            steps {
                 input 'Deploy To Production?'
                 milestone(1)
                script {
                    sh "sshpass -p 'Mummy@786' -v ssh -o StrictHostKeyChecking=no root@192.168.56.107 \"docker pull rajendrakumarm/train-schedule:${env.BUILD_NUMBER}\""
                    try  { 
                        sh "sshpass -p 'Mummy@786' -v ssh -o StrictHostKeyChecking=no root@192.168.56.107 \"docker stop train-schedule\""
                        sh "sshpass -p 'Mummy@786' -v ssh -o StrictHostKeyChecking=no root@192.168.56.107 \"docker rm train-schedule\""
                    } catch (err) {
                        echo: 'caught error: $err'
                    }
                    sh "sshpass -p 'Mummy@786' -v ssh -o StrictHostKeyChecking=no root@192.168.56.107 \"docker run --restart always --name train-schedule -p 8080:8080 -d  rajendrakumarm/train-schedule:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
  }
