pipeline {
    environment {
        IMAGE_NAME = "helloworld"
        IMAGE_TAG = "latest"
        STAGING = "joris-staging"
        PRODUCTION = "joris-production"
        IMAGE_REPO = "joriscommin"
     }
    agent none
    stages {
      stage('Build Image') {
            agent any
            steps {
                script {
                    echo 'Building..'
                    sh 'docker build -t ${IMAGE_REPO}/${IMAGE_NAME}:${IMAGE_TAG} .'
                }
            }
        }
        stage('Run container based on builded image') {
            agent any
            steps {
               script {
                 sh '''
                    docker run --name ${IMAGE_NAME} -d -p 80:80 ${IMAGE_REPO}/${IMAGE_NAME}:${IMAGE_TAG}
                    sleep 5
                 '''
               }
            }
        }
        stage('Test image') {
            agent any
            steps {
                script {
                        sh '''
                        curl http://172.17.0.1 | grep -q "Hello universe"
                        '''
              }
           }
        }
        stage('Cleaning Container') {
            agent any
            steps {
                script {
                    sh 'docker rm -vf ${IMAGE_NAME}'
                }
            }
        }
        stage('Push image on dockerhub') {
           agent any
           environment {
                DOCKERHUB_LOGIN = credentials('dockerhub_joriscommin')
            }
           steps {
               script {
                   sh '''
                   docker login --username ${DOCKERHUB_LOGIN_USR} --password ${DOCKERHUB_LOGIN_PSW}
                   docker push ${IMAGE_REPO}/${IMAGE_NAME}:${IMAGE_TAG}
                   '''
  }
           }
      }
}
}
