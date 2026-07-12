pipeline {
    agent {
        kubernetes {
            yaml '''
            ...
            '''
        }
    }

    triggers {
        pollSCM('H/1 * * * *')
    }

    environment {
        REPO_URL        = 'https://github.com/Abdelaziz-Gbr/nodejs.git'
        BRANCH_NAME     = 'main'
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
                    --dockerfile=${WORKSPACE}/Dockerfile \
                    --destination=docker.io/${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} \
                    --destination=docker.io/${DOCKER_HUB_USER}/${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }
}