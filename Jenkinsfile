pipeline {
    agent { label 'agent1' }

    environment {
        SCANNER_HOME = tool 'sonar'
        TARGET_URL = "http://localhost:8000"
    }

    stages {

        stage('Git Clone') {
            steps {
                git url: 'https://github.com/aman-yadavvv/django-webapp.git', branch: 'master'
            }
        }

        stage('Gitleaks Scan') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                    sh 'gitleaks detect -s . -c .gitleaks.toml -r gitleaks-report.txt || true'
                }
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs . --format table -o trivy-report.html || true'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                      -Dsonar.projectName=BG \
                      -Dsonar.projectKey=BG \
                      -Dsonar.python.version=3 \
                      -Dsonar.exclusions=**/*.html,**/*.mp4,**/media/**
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t django-website ./django_web_app'
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image django-website --format table -o trivy-report-image.html || true'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    docker login -u ${USER} -p ${PASS}
                    docker tag django-website ${USER}/django-website:latest
                    docker push ${USER}/django-website:latest
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: '**/gitleaks-report.txt, **/trivy-report.html, **/trivy-report-image.html, **/zap-report.html', allowEmptyArchive: true

            emailext(
                from: "ay7509737@gmail.com",
                to: "ay7509737@gmail.com",
                subject: "✅ Pipeline Success: Jenkins Reports",
                body: """
                    Hello Team,

                    The Jenkins pipeline has completed successfully.

                    Please see the attached reports.
                """,
                attachmentsPattern: "gitleaks-report.txt,trivy-report.html,trivy-report-image.html,zap-report.html"
            )
        }

        failure {
            archiveArtifacts artifacts: '**/gitleaks-report.txt, **/trivy-report.html, **/trivy-report-image.html, **/zap-report.html', allowEmptyArchive: true

            emailext(
                from: "ay7509737@gmail.com",
                to: "ay7509737@gmail.com",
                subject: "❌ Pipeline Failure: Jenkins Reports",
                body: """
                    Hello Team,

                    The Jenkins pipeline has failed.

                    Please check the attached reports for details.
                """,
            attachmentsPattern: "gitleaks-report.txt,trivy-report.html,trivy-report-image.html,zap-report.html"
            )
        }
    }
}
