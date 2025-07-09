pipeline {
    agent any

    tools {
        jdk 'jdk'
        nodejs 'node'
    }

    environment {
        SONAR_SCANNER_HOME = "${env.SONAR_SCANNER_HOME}"
        SONAR_TOKEN = credentials('sonar-token')
        NVD_API_KEY = credentials('nvd-api-key')
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
                        bat "${SONAR_SCANNER_HOME}\\bin\\sonar-scanner -Dsonar.projectKey=bookstore-backend -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.token=${SONAR_TOKEN}"
                    }
                }
                dir('frontend') {
                    withSonarQubeEnv('SonarQube') {
                        bat "${SONAR_SCANNER_HOME}\\bin\\sonar-scanner -Dsonar.projectKey=bookstore-frontend -Dsonar.sources=src -Dsonar.host.url=http://localhost:9000 -Dsonar.token=${SONAR_TOKEN}"
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('backend') {
                    bat 'npm install'
                }
                dir('frontend') {
                    bat 'npm install'
                }
            }
        }

        stage('OWASP FS SCAN (Docker)') {
            steps {
                script {
                    bat """
                    docker run --rm ^
                        -v "%cd%:/src" ^
                        owasp/dependency-check:latest ^
                        --project "bookstore-mern" ^
                        --scan /src ^
                        --format HTML ^
                        --out /src/odc-report ^
                        --nvdApiKey ${NVD_API_KEY} ^
                        --nvdApiDelay 2000
                    """
                }
                archiveArtifacts artifacts: 'odc-report/dependency-check-report.html', onlyIfSuccessful: true
            }
        }

        stage('TRIVY FS SCAN') {
            steps {
                bat 'trivy fs . > trivy-fs.txt'
                archiveArtifacts artifacts: 'trivy-fs.txt', onlyIfSuccessful: true
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        dir('frontend') {
                            bat 'docker build -t bookstore-frontend -f Dockerfile .'
                            bat 'docker tag bookstore-frontend mehedibu2013/bookstore-frontend:latest'
                            bat 'docker push mehedibu2013/bookstore-frontend:latest'
                        }
                        dir('backend') {
                            bat 'docker build -t bookstore-backend -f Dockerfile .'
                            bat 'docker tag bookstore-backend mehedibu2013/bookstore-backend:latest'
                            bat 'docker push mehedibu2013/bookstore-backend:latest'
                        }
                    }
                }
            }
        }

        stage('TRIVY Image Scan') {
            steps {
                bat 'trivy image mehedibu2013/bookstore-frontend:latest > trivy-image-frontend.txt'
                bat 'trivy image mehedibu2013/bookstore-backend:latest > trivy-image-backend.txt'
                archiveArtifacts artifacts: 'trivy-image-*.txt', onlyIfSuccessful: true
            }
        }

        stage('Deploy to Kind') {
            steps {
                bat 'kind load docker-image mehedibu2013/bookstore-frontend:latest --name bookstore-cluster'
                bat 'kind load docker-image mehedibu2013/bookstore-backend:latest --name bookstore-cluster'
                bat 'kubectl apply -f manifest.yml'
            }
        }
    }

    post {
        always {
            echo "Pipeline completed with status: ${currentBuild.currentResult}"
            archiveArtifacts artifacts: '**/*.txt, **/*.html', onlyIfSuccessful: true
        }
        failure {
            echo "Build failed ❌"
        }
        success {
            echo "Build succeeded ✅"
        }
    }
}
