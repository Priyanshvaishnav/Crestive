pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/Priyanshvaishnav/Crestive.git' // Replace with your GitHub repo link
        DOCKER_IMAGE = 'priyansh21/crestive'  
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub repository...'
                git url: "${GIT_REPO}", branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Scan for Vulnerabilities') {
            steps {
                echo 'Scanning Docker image for vulnerabilities...'
                sh "trivy image --format table -o trivy-report.html ${DOCKER_IMAGE}:${DOCKER_TAG}"

                archiveArtifacts artifacts: 'trivy-report.html', fingerprint: true
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: 'DockerHub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo ${DOCKER_PASSWORD} | docker login --username ${DOCKER_USERNAME} --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
