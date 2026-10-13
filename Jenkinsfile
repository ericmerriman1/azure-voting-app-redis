pipeline {
    agent any

    environment {
        // Prepend your custom path containing the grype binary to the existing PATH
        PATH = "/opt/homebrew/bin:${env.PATH}"
    }

    stages {
        stage('Verify Branch') {
            steps {
                echo "${GIT_BRANCH}"
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    DOCKER_DEFAULT_PLATFORM=linux/amd64 /opt/homebrew/bin/docker compose build
                '''
            }
        }

        stage('Start App') {
            steps {
                sh '''
                    DOCKER_DEFAULT_PLATFORM=linux/amd64 /opt/homebrew/bin/docker compose up -d
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    /opt/homebrew/bin/pytest ./tests/test_sample.py
                '''
            }
            post {
                success {
                    echo 'Tests passed! :'
                }
                failure {
                    echo 'Tests failed ::'
                }
            }
        }

        stage('Run Grype') {
            agent {
                label 'build-1'
            }
            steps {
                sh '''
                    /opt/homebrew/bin/grype docker:ericmerriman/jenkins-tutorial:2026 -o json > grype.json
                '''

                sh '''
                    echo "--- Critical Vulnerability Summary ---"
                    jq -r '.matches[] | select(.vulnerability.severity == "Critical") | .artifact.name + " (" + .vulnerability.id + ")"' grype.json

                    CRIT_COUNT=$(jq '[.matches[] | select(.vulnerability.severity == "Critical")] | length' grype.json)
                    echo "Total critical vulnerabilities: ${CRIT_COUNT}"

                    if [ "${CRIT_COUNT}" -gt 0 ]; then
                        echo "Found ${CRIT_COUNT} critical vulnerabilities."
                        exit 0
                    fi
                '''
         }
      }    
   }

    post {
        always {
            sh '''
                DOCKER_DEFAULT_PLATFORM=linux/amd64 /opt/homebrew/bin/docker compose down
            '''
        }
   }
}

