pipeline {
   agent any

   environment {
        // Define the full path to the docker binary
        DOCKER_BIN = '/opt/homebrew/bin/docker'
        
        // Define your image name and registry
        IMAGE_NAME = 'ericmerriman/jenkins-tutorial:2026'
        
        // Jenkins Credentials ID for your Docker registry username/password or token
        DOCKER_CREDS_ID = 'dockerhub'
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
/*          echo "Runnning in $WORKSPACE"
            dir("$WORKSPACE/azure-vote") {
               script {
                  docker.withRegistry('', 'dockerhub') {
                     def image = docker.build('ericmerriman/jenkins-tutorial:2026')
                     image.push()
                  }


               }
            }
*/
            withCredentials([usernamePassword(
               credentialsId: env.DOCKER_CREDS_ID,
               usernameVariable: 'DOCKER_USER',
               passwordVariable: 'DOCKER_PASS'
            )]) {
               script {
                  sh "docker build -t ${IMAGE_NAME} ./azure-vote"
                  // Authenticate using the full docker binary path
                  sh "echo '${env.DOCKER_PASS}' | ${DOCKER_BIN} login --username '${env.DOCKER_USER}' --password-stdin"
                        
                  // Push the image
                  sh "${DOCKER_BIN} push ${IMAGE_NAME}"
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
