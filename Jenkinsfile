pipeline {
  environment {
    registry = "bkshashi9/webapp"
    registryCredential = 'dockerhub'
    dockerImage = ''

  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git 'ssh://git@18.139.110.189:2222/git-server/repos/webapp.git'
      }
    }
    stage('Building Docker image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          
        }
      }
    }
    stage('Push Image to Docker Hub ') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            dockerImage.push('latest')
          }
        }
      }
    }


stage ('Deploy') {
    steps{
        sshagent(credentials : ['ubuntu']) {
            sh 'docker pull bkshashi9/webapp:latest'
            sh 'docker stop webapp'
            sh 'docker rm webapp'
            sh 'docker rmi bkshashi9/webapp:current || true'
            sh 'docker tag bkshashi9/webapp:latest bkshashi9/webapp:current'
            sh 'docker run -d --name webapp -p 8082:80 bkshashi9/webapp:latest'
        }
    }
}

stage('Remove Unused docker image') {
      steps{
sshagent(credentials : ['ubuntu']) {       
 sh "docker rmi $registry:$BUILD_NUMBER"
      }
}
    }

      
    }

   
  }


