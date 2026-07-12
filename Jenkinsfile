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
    tty: true
    volumeMounts:
    - name: docker-config
      mountPath: /kaniko/.docker

  volumes:
  - name: docker-config
    secret:
      secretName: docker-creds
      items:
      - key: .dockerconfigjson
        path: config.json
'''
        }
    }

    triggers {
        pollSCM('H/1 * * * *')
    }

    environment {
        DOCKER_HUB_USER = 'abdelazizgbr'
        IMAGE_NAME      = 'nodejs-app'
        IMAGE_TAG       = "${BUILD_NUMBER}"
    }

    stages {
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