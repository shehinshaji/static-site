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
//            JAVA_HOME = "/opt/java/openjdk" 
        }
    stages {
        stage("Analysis") {
            agent {
                docker {
                    image "ubuntu:${IMAGE_TAG}"
                    reuseNode true
                    args '-u root -v /opt/java:/opt/java -v /var/run/docker.sock:/var/run/docker.sock -v /var/jenkins_home/tools:/var/jenkins_home/tools'
                }
            }
            stages {
//                    stage("dependency setup") {
//                        steps {
//                                sh script: 'apt update && apt install docker.io curl openjdk-11-jdk -y', label: 'dependencies installation'
//                                sh script: 'curl -fsSL https://deb.nodesource.com/setup_18.x | bash -'
//                                sh script: 'DEBIAN_FRONTEND=noninteractive apt install -y nodejs'
//                                sh script: "echo 'export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64' >> ~/.bashrc"
//                                sh script: "echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc"
//                                sh script: 'source ~/.bashrc'
//                        }
//                    }

//                    stage('Install Trivy') {
//                        steps {
//                            script {
//                                sh '''
//                                apt-get install wget apt-transport-https gnupg lsb-release -y
//                                wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | tee /usr/share/keyrings/trivy.gpg > /dev/null
//                                echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | tee -a /etc/apt/sources.list.d/trivy.list
//                                apt-get update -y
//                                apt-get install trivy -y
//                                '''
//                            }
//                        }
//                    }

//                    stage("docker image build") {
//                        steps {
//                                sh script: 'sed -i "/^FROM/a LABEL BUILD_ID=${BUILD_ID}" Dockerfile'
//                                sh script: 'docker build --no-cache -t ${BUILD_ID} .', label: 'docker image build'
//                        }
//                    }
                    
//                    stage('Scan Docker Image for Vulnerabilities') {
//                        steps {
//                            script {
//                                sh 'trivy image -f json -o trivy-report.json ${BUILD_ID}'
//                            }
//                        }
//                    }

//                    stage("Dependency Check") {
//                        steps {
//                            dependencyCheck additionalArguments: '--scan ./ --format JSON --out ./dependency-check-report.json', odcInstallation: 'OWASP Dependency-Check'
//                            dependencyCheck additionalArguments: ''' 
//                                        -o './'
//                                        -s './'
//                                        -f 'ALL' 
//                                        --prettyPrint''', odcInstallation: 'OWASP Dependency-Check'
        
                            
//                            dependencyCheckPublisher pattern: 'dependency-check-report.xml'
//                        }
//                    }
                                      
                    stage('Feature branch Trigger') {
                        when {
                           branch "test/email"
                        }
                        steps {
                                sh 'echo "Trigger on Feature branch." '
                        }
                    }

                    stage('Dev branch Trigger') {
                        when {
                           branch "dev"
                        }
                        steps {
                                sh 'echo "Trigger on develop branch." '
                        }
                    }
 
                    stage('Main branch Trigger') {
                        when {
                           branch "main"
                        }
                        steps {
                                sh 'echo "Trigger on main branch." '
                        }
                    }
               
            }
                  
//            post {
//                always {
//                    withChecks('dependency-check') {
//                        recordIssues(
//                            publishAllIssues: true,
//                            enabledForFailure: true,
//                            aggregatingResults: true,
//                            tools: [owaspDependencyCheck(pattern: '**/dependency-check-report.json')],
//                            qualityGates: [[threshold: 1, type: 'TOTAL', unstable: true]]
//                        )
//                    }
                    
//                    withChecks('trivy-scan') {
//                        recordIssues(
//                            publishAllIssues: true,
//                            enabledForFailure: true,
//                            aggregatingResults: true,
//                            tools: [trivy(pattern: '**/trivy-report.json')],
//                            qualityGates: [[threshold: 1, type: 'TOTAL', unstable: true]]
//                        )
//                    }
//                }
//            }  

        }
    }

    post {
        always {
        emailext (
            //subject: "${env.PROJECT_NAME} - Build # ${env.BUILD_NUMBER} - ${env.BUILD_STATUS}!",
            subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!',
            //body: """${env.PROJECT_NAME} - Build # ${env.BUILD_NUMBER} - ${env.BUILD_STATUS}:
            body: 'Project: $PROJECT_NAME - Build No.: # $BUILD_NUMBER - Build Status: $BUILD_STATUS:' +
            '\nCheck console output at $BUILD_URL to view the results.',
            recipientProviders: [developers(), requestor()],
            to: '$DEFAULT_RECIPIENTS'  // Add the default recipients token
        )
    }
        cleanup {
//            sh 'docker rmi $(docker images -q -f "label=BUILD_ID=${BUILD_ID}")'
            cleanWs deleteDirs: true
        }
    }
}
