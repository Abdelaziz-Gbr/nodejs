pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git(branch: 'main', credentialsId: 'github', url: 'https://github.com/Abdelaziz-Gbr/nodejs.git')
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
      }
    }

    stage('Login to DockerHub') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }

    stage('Push to DockerHub') {
      steps {
        sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
      }
    }

  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    IMAGE_NAME = 'abdelazizgbr/iti-node-app'
  }
  post {
    success {
      echo "✅ Build and push successful! Image: $IMAGE_NAME:$BUILD_NUMBER"
    }

    failure {
      echo '❌ Build or push failed.'
    }

  }
}