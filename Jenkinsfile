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
	stage('Install Packages') {
      steps {
		sh 'wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb'
		sh 'sudo dpkg -i packages-microsoft-prod.deb'
		sh 'sudo apt-get install apt-transport-https'
		sh 'sudo apt-get update'
		sh 'sudo apt-get install dotnet-sdk-2.2'
      }
    }
	stage('Build') {
      steps {
		sh 'dotnet build eShopOnWeb.sln'
      }
    }
    stage('Building image') {
		steps {
			echo 'Build docker image'

			script {
				//dockerImage = docker.build registry + ":latest"
				sh 'docker build --pull -t web -f src/Web/Dockerfile.jenkins .'
			}
		}
		//docker build --pull -t web -f src/Web/Dockerfile .

		//agent {
			//dockerfile {
				//filename 'src/Web/Dockerfile.jenkins'
				//additionalBuildArgs '--pull -t web'
			//}
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