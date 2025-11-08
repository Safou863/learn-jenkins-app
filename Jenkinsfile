pipeline {
    agent any
    environment{
        NETLIFY_SITE_ID = '113aac2d-ceff-490b-af8a-673c3e0b72df'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        }


    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }            
            steps {
                sh '''
                test -f build/index.html
                npm test
                '''
            }
            }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status

                '''
            }
        }
    }

    post{
        always{
            junit 'test-results/junit.xml'
            }

    }
}
