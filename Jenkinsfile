pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - sleep
    args:
    - 99d
    volumeMounts:
    - name: docker-config
      mountPath: /kaniko/.docker

  volumes:
  - name: docker-config
    secret:
      secretName: docker-creds
      items:                  # <-- ADD THIS SECTION
      - key: .dockerconfigjson
        path: config.json     # <-- This forces the filename to be exactly config.json
'''
        }
    }

    environment {
        REPO_URL        = 'https://github.com/Abdelaziz-Gbr/nodejs.git'
        BRANCH_NAME     = 'master'
        DOCKER_HUB_USER = 'abdelazizgbr'
        IMAGE_NAME      = 'nodejs-app'
        IMAGE_TAG       = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: "${BRANCH_NAME}", url: "${REPO_URL}"
            }
        }

        stage('Build and Push with Kaniko') {
            steps {
                container('kaniko') {
                    sh """
                    /kaniko/executor \
                    --context=${WORKSPACE} \
                    --dockerfile=${WORKSPACE}/dockerfile \
                    --destination=docker.io/${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} \
                    --destination=docker.io/${DOCKER_HUB_USER}/${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }
}