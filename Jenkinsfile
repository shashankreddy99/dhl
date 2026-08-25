pipeline {
    agent any

    tools {
        nodejs 'node20'
    }

    environment {
        SONARQUBE_SERVER_ENV = 'sonarqubeserver'
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_CREDITANLS_ID = "docker-creds"
        DOCKER_IMAGE = "shashanka00315964/dhl_repo"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
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
                    from: '',
                    replyTo: '',
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
            }
        }

        stage('DependencyResoluction') {
            steps {
                sh 'npm ci'
            }
        }

        stage('lint check') {
            steps {
                sh 'npm run lint'
            }
        }

        stage('SonarqubeStaicScan') {
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

        stage('Security_scan_trivy_filesystem') {
            steps {
                echo 'Scan the project workspace for vulnerabilities'
               // sh 'trivy fs --exit-code 0 --severity HIGH,CRITICAL --format table .'
                sh 'trivy fs --scanners vuln --severity HIGH,CRITICAL .'
            }
        }
       /*
        stage("Build_Docker_image") {
            steps{
                echo "Building docker image: ${DOCKER_IMAGE}:${IMAGE_TAG}.... "
                sh "docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} ."
                sh "docker tag ${DOCKER_IMAGE}:${IMAGE_TAG} ${DOCKER_IMAGE}:latest"
            }
        }
    }
   */

    post {
        failure {
            slackSend(
                channel: '#thesumari',
                tokenCredentialId: 'slack-token',
                message: 'Pipeline Failed'
            )

            mail(
                bcc: 'k.shashankreddy599@gmail.com',
                body: 'Pipeline Failed',
                cc: 'k.shashankreddy599@gmail.com',
                from: '',
                replyTo: '',
                subject: 'Build Failed',
                to: 'k.shashankreddy599@gmail.com'
            )
        }
    }
}