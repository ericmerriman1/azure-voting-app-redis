pipeline {
   agent any

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
            grypeScan autoInstall: false, repName: 'grypeReport_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:ericmerriman/jenkins-tutorial:2026'
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
