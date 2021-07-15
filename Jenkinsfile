pipeline {

  agent none

  environment {
    DOCKER_IMAGE = "kienle/flask-docker"
    registry = "YourDockerhubAccount/YourRepository" 
   registryCredential = 'docker-hub'        
  }

  stages {
    stage("Test") {
      agent {
          docker {
            image 'python:3.8-slim-buster'
            args '-u 0:0 -v /tmp:/root/.cache'
          }
      }
      steps {
        sh "pip install poetry"
        sh "poetry install"
        sh "poetry run pytest"
      }
    }
  
  stage('Building our image') { 
      steps { 
          script { 
              dockerImage = docker.build registry + ":$BUILD_NUMBER" 
          }
      } 
    }
  stage('Deploy our image') { 
      steps { 
          script { 
              docker.withRegistry( '', registryCredential ) { 
                  dockerImage.push() 
              }
          } 
      }
    } 
  stage('Cleaning up') { 
      steps { 
          sh "docker rmi $registry:$BUILD_NUMBER" 
      }
    } 

  post {
    success {
      echo "SUCCESSFUL"
    }
    failure {
      echo "FAILED"
    }
  }
}
}
