#!groovy

pipeline {
    agent any
    options {
        disableConcurrentBuilds(abortPrevious: true)
    }
    environment {
            APP_NAME = "${env.MY_APP_NAME}"
            BUILD_ID = "${APP_NAME}:${BUILD_NUMBER}"
            IMAGE_TAG = "latest"
            CAPROVER_SERVER = "${env.CAPROVER_SERVER}"
            CAPROVER_TOKEN = "${env.CAPROVER_TOKEN}"
            CAPROVER_APP = "${env.CAPROVER_APP}"
            DOCKER_USER = "${env.DOCKER_USER}"
            DOCKER_PASSWORD = "${env.DOCKER_PASSWORD}"
        }
    stages {
        stage("Analysis") {
            agent {
                docker {
                    image "ubuntu:${IMAGE_TAG}"
                    reuseNode true
                }
            }
            stages {
                    stage("dependency setup") {
                        steps {
                                sh script: 'apt update && apt install docker.io -y', label: 'dependencies installation'
                        }
                    }

                    stage("docker image build") {
                        steps {
                                sh 'sed -i "/^FROM/a LABEL BUILD_ID=${BUILD_ID}" Dockerfile'
                                sh 'docker build --no-cache -t ${BUILD_ID} .', label: 'docker image build'
                        }
                    }
                    stage('docker login and build push') {
                        steps {
                                sh script: 'docker login -u ${DOCKER_USER} -p ${DOCKER_PASSWORD}', label: 'docker login'
                                sh script: 'docker push ${BUILD_ID}', label: 'docker image push'
                        }
                    }
                    stage("Deploy") {
                        steps {
                                sh script: 'npm i -g caprover', label: 'caprover installation'
                                sh script: 'caprover deploy --caproverUrl ${CAPROVER_SERVER} --appToken ${CAPROVER_TOKEN} --appName ${CAPROVER_APP} -i ${BUILD_ID}', label: 'app deployment'
                        }
                    }
            }
        }
    }

    post {
        cleanup {
            sh 'docker rmi $(docker images -q -f "label=BUILD_ID=${BUILD_ID}")'
            cleanWs deleteDirs: true
        }
    }
}
