pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "kassas/first_flask_app"
        DOCKER_CREDENTIALS_ID = 'kassas'
        GITHUB_REPO = 'https://github.com/melkassas123/simple_flask_app'
    }

    stages {
        stage('Run Security Tests') {
            steps {
                script {
                    echo 'Running security tests...'
                    sh 'pip install bandit'
                    sh 'bandit -r .'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }

        stage('Run Security Scans with Trivy') {
            steps {
                script {
                    echo 'Installing Trivy...'
                    sh '''
                    if ! command -v trivy &> /dev/null
                    then
                        wget https://github.com/aquasecurity/trivy/releases/download/v0.40.0/trivy_0.40.0_Linux-64bit.deb
                        sudo dpkg -i trivy_0.40.0_Linux-64bit.deb
                    fi
                    '''

                    echo 'Running Trivy security scan...'
                    // Run Trivy security scan
                    sh 'trivy image ${DOCKER_IMAGE}'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    withDockerRegistry(credentialsId: "${DOCKER_CREDENTIALS_ID}") {
                        sh 'docker push ${DOCKER_IMAGE}'
                    }
                }
            }
        }
    }

    post {
        always {
            sh 'docker rmi ${DOCKER_IMAGE} || true'
        }
    }
}

