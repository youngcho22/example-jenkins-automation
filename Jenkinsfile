pipeline {
    agent {
        docker { image 'node' }
    }
    environment {
        TESTMO_URL = 'https://youngcho-instabase.testmo.net/'
        TESTMO_TOKEN = 'testmo_api_eyJpdiI6IjJLL0IweEl4TWlxaXZpNnJwaHFWL2c9PSIsInZhbHVlIjoiZS9SYU9BeEV1bWZCT2Z6M1Nua1E3RmozOFZpY042M1JKWkJ3dnhaSDlFQT0iLCJtYWMiOiI3NTI3NTkzZGJlNDgzNTI2ZmMyZTI0YzU3NWJkMWU0OWI0ZTc4OTdjYzE5M2MyNjA1NmEzYzM4OGNkODkwMmQxIiwidGFnIjoiIn0='
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building ..'
            }
        }
        stage('Test') {
            environment {
                GIT_SHORT_COMMIT = "${GIT_COMMIT[0..7]}"
            }
            steps {
                sh 'npm ci'
                sh 'npm install --no-save @testmo/testmo-cli'

                // Optionally add a couple of fields such as the
                // git hash and link to the build
                sh '''
                  npx testmo automation:resources:add-field --name git --type string \
                    --value $GIT_SHORT_COMMIT --resources resources.json
                  npx testmo automation:resources:add-link --name build \
                    --url $BUILD_URL --resources resources.json
                '''

                sh '''
                  npx testmo automation:run:submit \
                    --instance $TESTMO_URL \
                    --project-id 1 \
                    --name "Mocha test run" \
                    --source "unit-tests" \
                    --resources resources.json \
                    --results results/*.xml \
                    -- npm run mocha-junit # Note space after --
                '''
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying ..'
            }
        }
    }
    post {
        always {
            junit 'results/*.xml'
        }
    }
}
