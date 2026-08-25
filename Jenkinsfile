pipeline {
    agent any

    tools {
        nodejs 'node20'
    }

    environment {
        SONARQUBE_SERVER_ENV = 'sonarqubeserver'
        DOCKER_IMAGE = 'shashanka00315964/dhl_repo'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('CheckoutSourceCode') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shashankreddy99/dhl.git'

                slackSend(
                    channel: '#thesumari',
                    tokenCredentialId: 'slack-token',
                    message: 'Checkout Successful'
                )

                mail(
                    bcc: 'k.shashankreddy599@gmail.com',
                    body: 'Checkout Source Code successful',
                    cc: 'k.shashankreddy599@gmail.com',
                    subject: 'Build successful',
                    to: 'k.shashankreddy599@gmail.com'
                )
            }
        }

        stage('Environment setup') {
            steps {
                echo 'Checking workspace and tools'

                sh 'node --version'
                sh 'npm --version'
                sh 'docker --version'
                sh 'trivy --version'
            }
        }

        stage('DependencyResolution') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Lint Check') {
            steps {
                sh 'npm run lint'
            }
        }

        stage('SonarQube Static Scan') {
            steps {
                script {
                    def scannerHome = tool 'sonar-scanner'

                    withSonarQubeEnv(SONARQUBE_SERVER_ENV) {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }

                    timeout(time: 10, unit: 'MINUTES') {
                        def qg = waitForQualityGate()

                        if (qg.status != 'OK') {
                            error "Pipeline aborted: ${qg.status}"
                        }
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Security Scan - Trivy') {
            steps {
                echo 'Scanning project workspace for vulnerabilities'

                sh '''
                    trivy fs \
                    --scanners vuln \
                    --severity HIGH,CRITICAL \
                    .
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE}:${IMAGE_TAG}"

                sh """
                    docker build \
                    -t ${DOCKER_IMAGE}:${IMAGE_TAG} \
                    .
                """

                sh """
                    docker tag \
                    ${DOCKER_IMAGE}:${IMAGE_TAG} \
                    ${DOCKER_IMAGE}:latest
                """
            }
        }
    }

    post {
        success {
            slackSend(
                channel: '#thesumari',
                tokenCredentialId: 'slack-token',
                message: "Pipeline Successful - Build #${BUILD_NUMBER}"
            )

            mail(
                bcc: 'k.shashankreddy599@gmail.com',
                body: "Pipeline Successful - Build #${BUILD_NUMBER}",
                cc: 'k.shashankreddy599@gmail.com',
                subject: "Build Successful #${BUILD_NUMBER}",
                to: 'k.shashankreddy599@gmail.com'
            )
        }

        failure {
            slackSend(
                channel: '#thesumari',
                tokenCredentialId: 'slack-token',
                message: "Pipeline Failed - Build #${BUILD_NUMBER}"
            )

            mail(
                bcc: 'k.shashankreddy599@gmail.com',
                body: "Pipeline Failed - Build #${BUILD_NUMBER}",
                cc: 'k.shashankreddy599@gmail.com',
                subject: "Build Failed #${BUILD_NUMBER}",
                to: 'k.shashankreddy599@gmail.com'
            )
        }
    }
}