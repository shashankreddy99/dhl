pipeline {
    agent any

    tools {
        nodejs 'node20'
    }

    environment {
        SONARQUBE_SERVER_ENV = 'sonarqubeserver'
        DOCKER_REGISTRY       = 'docker.io'
        DOCKER_CREDENTIALS_ID = 'docker-creds'
        DOCKER_IMAGE_BASE    = 'dataquaintacademy/dhl'
        IMAGE_TAG            = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('CheckoutSourceCode') {
            steps {
                git branch: 'main', url: 'https://github.com/Quantumvector2026/dhl.git'
                slackSend channel: 'teamsamurai', message: 'Checkout Successful'
                mail bcc: 'projects2488@gmail.com', body: 'Checkout source code successful', cc: 'projects2488@gmail.com', from: '', replyTo: '', subject: 'Build successful', to: 'projects2488@gmail.com'
            }
        }

        stage("Environment Setup") {
            steps {
                echo "Checking workspace and tools"
                sh 'node --version'
                sh 'npm --version'
            }
        }

stage('Trivy Filesystem Scan') {
    steps {
        script {
            sh '''
            mkdir -p trivy-reports

            SERVICES="backend frontend banking-service language-service price-service air-cargo-service sea-cargo-service"

            for service in $SERVICES
            do
                echo "Scanning $service..."
                trivy fs \
                    --scanners vuln,secret,misconfig \
                    --severity HIGH,CRITICAL \
                    --format table \
                    --output trivy-reports/${service}-fs.txt \
                    ./$service
            done
            '''
        }
    }
}

        stage("Build & Publish Docker Images") {
            parallel {
                stage('Build Backend') {
                    steps {
                        script {
                            echo "Building backend image..."
                            sh "docker build -t ${DOCKER_IMAGE_BASE}-backend:${IMAGE_TAG} -f backend/Dockerfile ./backend"
                            sh "docker tag ${DOCKER_IMAGE_BASE}-backend:${IMAGE_TAG} ${DOCKER_IMAGE_BASE}-backend:latest"
                            
                            withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-backend:${IMAGE_TAG}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-backend:latest"
                            }
                        }
                    }
                }

                stage('Build Banking Service') {
                    steps {
                        script {
                            echo "Building banking-service image..."
                            sh "docker build -t ${DOCKER_IMAGE_BASE}-banking-service:${IMAGE_TAG} -f banking-service/Dockerfile ./banking-service"
                            sh "docker tag ${DOCKER_IMAGE_BASE}-banking-service:${IMAGE_TAG} ${DOCKER_IMAGE_BASE}-banking-service:latest"
                            
                            withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-banking-service:${IMAGE_TAG}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-banking-service:latest"
                            }
                        }
                    }
                }

                stage('Build Language Service') {
                    steps {
                        script {
                            echo "Building language-service image..."
                            sh "docker build -t ${DOCKER_IMAGE_BASE}-language-service:${IMAGE_TAG} -f language-service/Dockerfile ./language-service"
                            sh "docker tag ${DOCKER_IMAGE_BASE}-language-service:${IMAGE_TAG} ${DOCKER_IMAGE_BASE}-language-service:latest"
                            
                            withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-language-service:${IMAGE_TAG}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-language-service:latest"
                            }
                        }
                    }
                }

                stage('Build Price Service') {
                    steps {
                        script {
                            echo "Building price-service image..."
                            sh "docker build -t ${DOCKER_IMAGE_BASE}-price-service:${IMAGE_TAG} -f price-service/Dockerfile ./price-service"
                            sh "docker tag ${DOCKER_IMAGE_BASE}-price-service:${IMAGE_TAG} ${DOCKER_IMAGE_BASE}-price-service:latest"
                            
                            withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-price-service:${IMAGE_TAG}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-price-service:latest"
                            }
                        }
                    }
                }

                stage('Build Air Cargo Service') {
                    steps {
                        script {
                            echo "Building air-cargo-service image..."
                            sh "docker build -t ${DOCKER_IMAGE_BASE}-air-cargo-service:${IMAGE_TAG} -f air-cargo-service/Dockerfile ./air-cargo-service"
                            sh "docker tag ${DOCKER_IMAGE_BASE}-air-cargo-service:${IMAGE_TAG} ${DOCKER_IMAGE_BASE}-air-cargo-service:latest"
                            
                            withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-air-cargo-service:${IMAGE_TAG}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-air-cargo-service:latest"
                            }
                        }
                    }
                }

                stage('Build Sea Cargo Service') {
                    steps {
                        script {
                            echo "Building sea-cargo-service image..."
                            sh "docker build -t ${DOCKER_IMAGE_BASE}-sea-cargo-service:${IMAGE_TAG} -f sea-cargo-service/Dockerfile ./sea-cargo-service"
                            sh "docker tag ${DOCKER_IMAGE_BASE}-sea-cargo-service:${IMAGE_TAG} ${DOCKER_IMAGE_BASE}-sea-cargo-service:latest"
                            
                            withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-sea-cargo-service:${IMAGE_TAG}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-sea-cargo-service:latest"
                            }
                        }
                    }
                }

                stage('Build Frontend') {
                    steps {
                        script {
                            echo "Building frontend image..."
                            sh "docker build -t ${DOCKER_IMAGE_BASE}-frontend:${IMAGE_TAG} -f frontend/Dockerfile ./frontend"
                            sh "docker tag ${DOCKER_IMAGE_BASE}-frontend:${IMAGE_TAG} ${DOCKER_IMAGE_BASE}-frontend:latest"
                            
                            withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USER --password-stdin ${DOCKER_REGISTRY}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-frontend:${IMAGE_TAG}"
                                sh "docker push ${DOCKER_IMAGE_BASE}-frontend:latest"
                            }
                        }
                    }
                }
            }
        }
    }

    post {
        failure {
            slackSend channel: 'teamsamurai', message: 'Pipeline Failed'
            mail bcc: 'projects2488@gmail.com', body: 'Pipeline Failed', cc: 'projects2488@gmail.com', from: '', replyTo: '', subject: 'Pipeline Failed!!', to: 'projects2488@gmail.com'
        }
    }
}
