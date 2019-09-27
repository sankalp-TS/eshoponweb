pipeline {
  //environment {
    //registry = "varma03/node-frontend"
    //registryCredential = 'dockerhub'
    //dockerImage = ''
  //}
  agent any
  
  stages {
    stage('Cloning Git') {
      steps {
        //Use if its Jenkins 'Pipeline Script' - (no Jenkinsfile is used).
		//git credentialsId: 'GitCred', url: 'https://github.com/sankalp-TS/eshoponweb.git'

		checkout scm
      }
    }
    stage('Building image') {
		steps {
			echo 'Build docker image'
		}
		//script {
			//dockerImage = docker.build registry + ":latest"
		   
			//docker build --pull -t web -f src/Web/Dockerfile .
			agent {
				dockerfile {
					filename 'src/Web/Dockerfile'
					additionalBuildArgs '--pull -t web'
				}
			}  
		//}
    }
    //stage('Deploy Image') {
      //steps{
        //script {
          //docker.withRegistry( '', registryCredential ) {
            //dockerImage.push()
          //}
        //}
      //}
    //}
    //stage('Remove Unused docker image') {
      //steps{
        //sh "docker rmi $registry:latest"
      //}
    //}
  }
}