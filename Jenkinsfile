pipeline {
  agent {label 'docker'} 
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t mikejc30/jenkins-nginx:batch3 .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker push mikejc30/jenkins-nginx:batch3'
      }
    }
    stage('Deploy') {
            steps {
              script {
                   sh "docker run --name=mywebapp1 -d -p 8084:80 mikejc30/jenkins-nginx:batch3"
                }
              }
            }
        }
    }
