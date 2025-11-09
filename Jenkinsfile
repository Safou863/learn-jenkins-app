pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '113aac2d-ceff-490b-af8a-673c3e0b72df'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build & Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    npm install -g netlify-cli
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    netlify deploy --dir=build --prod --auth $NETLIFY_AUTH_TOKEN --site $NETLIFY_SITE_ID
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
