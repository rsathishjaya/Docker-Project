pipeline {

  environment {
    registry = "sathishjaya/flask"
    registry_mysql = "sathishjaya/mysql"
    dockerImage = ""
  }

  agent any
    stages {
    stage('Checkout Source') {
      steps {
        git 'https://github.com/rsathishjaya/docker-project.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    
    
    stage('docker login') {
      steps{
        //withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
         // available as an env variable, but will be masked if you try to print it out any which way
         // note: single quotes prevent Groovy interpolation; expansion is by Bourne Shell, which is what you want
         // or inside double quotes for string interpolation
          //echo "username is $USERNAME"
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {    
         sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}" 

        }
    }
    }
  
    
    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('current') {
      steps{
        dir("${env.WORKSPACE}/mysql"){
          sh "pwd"
          }
      }
   }
   stage('Build mysql image') {
     steps{
       sh 'docker build -t "sathishjaya/mysql:$BUILD_NUMBER" "$WORKSPACE"/mysql'
       sh 'docker push "sathishjaya/mysql:$BUILD_NUMBER"'
        }
      }
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "frontend.yaml", kubeconfigId: "kube")
        }
      }
    }

  }

}
