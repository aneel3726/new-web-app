pipeline{
  agent { node { label 'globes' } } 
  tools {
      git 'git'
      maven 'maven'
  }
  environment {
    IMAGE_TAG = "${env.BUILD_NUMBER}"
    myImageName = "gampa"
    myDockerhub = "aneel123"
  }
  stages{
    stage('SCM Checkout'){
     steps{
          git 'https://github.com/aneel3726github/new-web-app.git'
          }
  }

    stage('Unit Testing'){
     steps{
             sh label: '', script: 'mvn clean test'
     }
   }
    stage('Maven packing'){
     steps{
           sh label: '', script: 'mvn clean package'
        }
    }
    stage('cleanup'){
     steps{
          sh '''
          pwd
          sudo sh ./clean_docker.sh
          '''
        }
    }
    stage('Build & Deploy New Image'){
     steps{
        withCredentials([usernamePassword(credentialsId: 'c5d27e0d-75a3-49be-b979-2c867f490307',usernameVariable: 'DOCKERUSERNAME', passwordVariable: 'DOCKERPASSWORD')]){
        script {
          sh '''
           sudo docker login -u ${ARTIUSERNAME} -p ${ARTIPASSWORD} docker.io
           #sudo docker images -a | grep ${myImageName} | awk '{print $3}' | xargs sudo docker rmi
           sudo docker build -t docker.io/${myDockerhub}/${myImageName}:${IMAGE_TAG} .
           sudo docker push docker.io/${myDockerhub}/${myImageName}:${IMAGE_TAG} 
           sudo docker images
           sudo docker ps -f name=${myImageName} -q | xargs --no-run-if-empty sudo docker container stop
           sudo docker container ls -a -fname=${myImageName} -q | xargs -r sudo docker container rm
           sudo docker system prune -f
           sudo docker run -d -p 8080:8080 --name ${myImageName} ${myDockerhub}/${myImageName}:${IMAGE_TAG}
           sudo docker logout 
           '''
        }
       }
     }
   }
  }
}
