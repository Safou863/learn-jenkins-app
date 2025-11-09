pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '113aac2d-ceff-490b-af8a-673c3e0b72df'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        NETLIFY_SITE_URL = "https://admirable-biscochitos-797d9d.netlify.app"
    }

    stages {
        stage('Build') { ... } // ton code build
        stage('Tests') { ... } // ton code tests
        stage('Deploy') { ... } // ton code deploy

        stage('Post Actions') {
            steps {
                echo "Pipeline terminé — Build, Tests, et Déploiement effectués."
                
                writeFile file: 'link.html', text: """
                    <a href="$NETLIFY_SITE_URL" target="_blank">
                        🔗 Voir le site en production
                    </a>
                """

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
            echo "Pipeline terminé."
        }
    }
}
