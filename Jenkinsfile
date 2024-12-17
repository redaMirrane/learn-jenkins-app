pipeline {
    agent {
        docker { image 'node:18-alpine' }
    }
    stages {
        stage('Check Node and NPM Versions') {
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
        stage('test'){
            steps {
                script {
                    sh 'npm test'
                }
            }
        }
    }
    post{
        always{
            junit 'test-results/junit.xml'
        }
    }
}
