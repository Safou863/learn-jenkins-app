pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '113aac2d-ceff-490b-af8a-673c3e0b72df'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        NETLIFY_SITE_URL = "https://admirable-biscochitos-797d9d.netlify.app"
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "=== BUILD STAGE ==="
                    ls -la
                    node --version
                    npm ci
                    npm run build
                    ls -la build
                '''
            }
        }

        stage('Tests') {
            parallel {
                stage('Unit Tests') {
                    agent {
                        docker {
                            image 'node:18'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo "=== UNIT TESTS ==="
                            npm test -- --ci --reporters=jest-junit
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E Tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo "=== E2E TESTS ==="
                            npm install serve
                            npx serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML(target: [
                                allowMissing: false,
                                alwaysLinkToLastBuild: true,
                                keepAll: true,
                                reportDir: 'playwright-report',
                                reportFiles: 'index.html',
                                reportName: 'Playwright Test Report'
                            ])
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "=== DEPLOY STAGE ==="
                    npm install netlify-cli
                    npx netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    npx netlify status
                    npx netlify deploy --prod --dir=build --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN
                '''
            }
        }

        stage('Post Actions') {
            steps {
                echo "Pipeline terminé — Build, Tests, et Déploiement effectués."
                
                // Crée un fichier HTML avec le lien vers le site
                writeFile file: 'link.html', text: """
                    <a href="$NETLIFY_SITE_URL" target="_blank">
                        🔗 Voir le site en production
                    </a>
                """

                // Publie ce fichier comme rapport HTML
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: 'link.html',
                    reportName: "Live Site Link"
                ])
            }
        }
    }

    post {
        always {
            echo "Pipeline terminé — Build, Tests, et Déploiement effectués."
        }
    }
}
