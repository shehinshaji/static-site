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
        }
    stages {
        stage("Analysis") {
            agent {
                docker {
                    image "ubuntu:${IMAGE_TAG}"
                    reuseNode true
                    args '-u root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            stages {
                    stage("dependency setup") {
                        steps {
                                sh script: 'apt update && apt install docker.io curl -y', label: 'dependencies installation'
                                sh script: 'curl -fsSL https://deb.nodesource.com/setup_18.x | bash -'
                                sh script: 'DEBIAN_FRONTEND=noninteractive apt install -y nodejs'
                        }
                    }

                    stage("docker image build") {
                        steps {
                                sh script: 'sed -i "/^FROM/a LABEL BUILD_ID=${BUILD_ID}" Dockerfile'
                                sh script: 'docker build --no-cache -t ${BUILD_ID} .', label: 'docker image build'
                        }
                    }
                    
                    stage("test stage") {
                        steps {
                                sh script: 'pwd && ls'
                        }
                    }

                    stage("Dependency Check") {
                        steps {
                            dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'OWASP Dependency-Check'
                        }
                    }
               
            }
                  
            post {
                always {
                    withChecks('dependency-check') {
                        recordIssues(
                            publishAllIssues: true,
                            enabledForFailure: true,
                            aggregatingResults: true,
                            tools: [owaspDependencyCheck(pattern: '**/dependency-check-report.xml')],
                            qualityGates: [[threshold: 1, type: 'TOTAL', unstable: true]]
                        )
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
