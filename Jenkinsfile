pipeline {
    agent any
    tools {
        jdk 'jdk'
        nodejs 'node'
    }
    environment {
        SONAR_SCANNER_HOME = "${env.SONAR_SCANNER_HOME}"
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/mehedibu2013/bookstore-mern.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                dir('backend') {
                    withSonarQubeEnv('SonarQube') {
                        bat "${SONAR_SCANNER_HOME}\\bin\\sonar-scanner -Dsonar.projectKey=bookstore-backend -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqa_f1e72a5cd4af6689d9d2f0d76d9c2b415decfe8a"
                    }
                }
                dir('frontend') {
                    withSonarQubeEnv('SonarQube') {
                        bat "${SONAR_SCANNER_HOME}\\bin\\sonar-scanner -Dsonar.projectKey=bookstore-frontend -Dsonar.sources=src -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqa_f1e72a5cd4af6689d9d2f0d76d9c2b415decfe8a"
                    }
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                dir('backend') {
                    bat "npm install"
                }
                dir('frontend') {
                    bat "npm install"
                }
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit --nvdApiKey d7e8c629-7da9-4f96-8a4a-a45fd3f213ba', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                bat "trivy fs . > trivyfs.txt"
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        dir('frontend') {
                            bat "docker build -t bookstore-frontend -f frontend/Dockerfile ."
                            bat "docker tag bookstore-frontend mehedibu2013/bookstore-frontend:latest"
                            bat "docker push mehedibu2013/bookstore-frontend:latest"
                        }
                        dir('backend') {
                            bat "docker build -t bookstore-backend -f backend/Dockerfile ."
                            bat "docker tag bookstore-backend mehedibu2013/bookstore-backend:latest"
                            bat "docker push mehedibu2013/bookstore-backend:latest"
                        }
                    }
                }
            }
        }
        stage('TRIVY Image Scan') {
            steps {
                bat "trivy image mehedibu2013/bookstore-frontend:latest > trivyimage-frontend.txt"
                bat "trivy image mehedibu2013/bookstore-backend:latest > trivyimage-backend.txt"
            }
        }
        stage('Deploy to Kind') {
            steps {
                script {
                    bat "kind load docker-image mehedibu2013/bookstore-frontend:latest --name bookstore-cluster"
                    bat "kind load docker-image mehedibu2013/bookstore-backend:latest --name bookstore-cluster"
                    bat "kubectl apply -f manifest.yml"
                }
            }
        }
    }
    post {
        always {
            echo "Pipeline completed with status: ${currentBuild.currentResult}"
        }
    }
}