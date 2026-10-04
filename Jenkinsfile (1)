// Fonction utilitaire : fonctionne sur Jenkins Windows (bat) et Linux (sh)
def run(String cmd) {
    if (isUnix()) {
        sh cmd
    } else {
        bat cmd
    }
}

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    script { run('mvn clean package -DskipTests') }
                }
            }
        }

        stage('Test Backend') {
            steps {
                dir('backend') {
                    script { run('mvn test') }
                }
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'backend/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    script {
                        run('npm install')
                        run('npm run build')
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    run("docker build -t appgestion-backend:${env.BUILD_NUMBER} -t appgestion-backend:latest backend")
                    run("docker build -t appgestion-frontend:${env.BUILD_NUMBER} -t appgestion-frontend:latest frontend")
                }
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
                archiveArtifacts artifacts: 'frontend/dist/**', allowEmptyArchive: true
            }
        }
    }

    post {
        success { echo 'Pipeline terminé avec succès.' }
        failure { echo 'Le pipeline a échoué, consulte les logs.' }
    }
}
