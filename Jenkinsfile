pipeline {
   agent any

   environment {
        // Prepend your custom path containing the grype binary to the existing PATH
        PATH = "/opt/homebrew/bin:${env.PATH}"
    }

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }
      stage('Docker Build') {
         steps {
            sh(script: '/opt/homebrew/bin/docker compose build')
         }
      }
      stage('Start App') {
         steps {
            sh(script: '/opt/homebrew/bin/docker compose up -d')
         }
      }
      stage('Run Tests') {
         steps {
            sh(script: '/opt/homebrew/bin/pytest ./tests/test_sample.py')
         }
         post {
            success {
               echo "Tests passed! :)"
            }
            failure {
               echo "Tests failed :("
            }
         }
      }
      stage('Run Grype') {
         agent {label 'build-1'}
         steps {
            sh(script: '/opt/homebrew/bin/grype ericmerriman/jenkins-tutorial:2026')
         }
         post {
            always {
               recordIssues(
                  tools: [grype()],
                  aggregatingResults: true,
               )
            }
         }
      }
   }
   post {
      always {
         sh(script: '/opt/homebrew/bin/docker compose down')
      }
   }
}
