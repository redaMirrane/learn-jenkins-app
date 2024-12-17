pipeline {
    agent none

    environment {
        PLAYWRIGHT_RESULTS_DIR = 'playwright-results'
    }

    stages {
        stage('Check Node and NPM Versions') {
            agent {
                docker { image 'node:18-alpine' }
            }
            steps {
                script {
                    sh '''
                        ls -la
                        node -v
                        npm -v
                        npm ci
                        npm run build
                        ls -la
                    '''
                }
            }
        }

        stage('Run Unit Tests') {
            agent {
                docker { image 'node:18-alpine' }
            }
            steps {
                script {
                    sh 'npm test'
                }
            }
        }

        stage('Run Playwright Tests') {
            agent {
                docker {
                    // Utilisation de l'image Playwright officielle
                    image 'mcr.microsoft.com/playwright:v1.40.0-jammy'
                    args '--ipc=host' // Permet de gérer les tests Playwright multi-processus
                }
            }
            steps {
                script {
                    sh '''
                        # Installation des dépendances si nécessaire
                        npm ci

                        # Exécution des tests Playwright avec stockage des résultats
                        mkdir -p ${PLAYWRIGHT_RESULTS_DIR}
                        npx playwright install --with-deps
                        npx playwright test --output=${PLAYWRIGHT_RESULTS_DIR}
                    '''
                }
            }
            post {
                always {
                    // Archiver les résultats des tests Playwright dans Jenkins
                    archiveArtifacts artifacts: "${PLAYWRIGHT_RESULTS_DIR}/**", allowEmptyArchive: true

                    // Publier le rapport HTML Playwright
                    publishHTML(target: [
                        reportDir: "${PLAYWRIGHT_RESULTS_DIR}",
                        reportFiles: 'index.html',
                        reportName: 'Playwright Test Results'
                    ])
                }
            }
        }
    }

    post {
        always {
            // Résultats des tests unitaires
            junit 'test-results/junit.xml'
        }
    }
}
