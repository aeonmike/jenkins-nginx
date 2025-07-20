pipeline {
  agent { label 'k8master' }

  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }

  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    SONARQUBE_ENV = 'sonarqube' // Jenkins name of SonarQube server
    DOCKER_REPO = 'mikecabalin09/mikejc30'
    CONTAINER_NAME = 'webapp-container'
  }

  stages {
    stage('Prepare') {
      steps {
        script {
          env.IMAGE_TAG = "build-${env.BUILD_NUMBER}"
          env.IMAGE_NAME = "${env.DOCKER_REPO}:${env.IMAGE_TAG}"
        }
      }
    }

    stage('SonarQube Scan') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh 'sonar-scanner \
              -Dsonar.projectKey=devops2025-sonar \
              -Dsonar.sources=. \
              -Dsonar.host.url=$SONAR_HOST_URL \
              -Dsonar.login=$SONAR_AUTH_TOKEN'
        }
      }
    }

    stage('Build') {
      steps {
        sh 'docker build -t $IMAGE_NAME .'
      }
    }

    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }

    stage('Push') {
      steps {
        sh 'docker push $IMAGE_NAME'
      }
    }

    stage('Deploy') {
      steps {
        script {
          sh 'kubectl create -f deployment.yaml'
        }
      }
    }
  }
}
