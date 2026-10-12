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
            sh(script: '/opt/homebrew/anaconda3/bin/pytest ./tests/test_sample.py')
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
      stage('Docker Push') {
         steps {
            echo "Runnning in $WORKSPACE"
            dir("$WORKSPACE/azure-vote") {
               script {
                  docker.withRegistry('', 'dockerhub') {
                     def image = docker.build('ericmerriman/jenkins-tutorial:2026')
                     image.push()
                  }
               }
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
