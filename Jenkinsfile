pipeline {
	//environment {
		//registry = "varma03/node-frontend"
		//registryCredential = 'dockerhub'
		//dockerImage = ''
	//}
	
	//Docker hub repo name
	//def registry = "sankalpreddy/eshoponweb"
	
	agent any

	options {
		skipDefaultCheckout false
		//buildDiscarder(logRotator(numToKeepStr: '5'))
	}
  
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
		//sh 'whoami'
		sh 'wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb'
		sh 'sudo dpkg -i packages-microsoft-prod.deb'

		sh 'sudo apt-get update'
		sh 'sudo apt-get install apt-transport-https'
		sh 'sudo apt-get update'
		sh 'sudo apt-get -y install dotnet-sdk-2.2'
      }
    }
	stage('Build') {
      steps {
		sh 'dotnet build eShopOnWeb.sln'
      }
    }
	stage('Unit Tests') {
      steps {
		sh 'pwd'
		sh 'dotnet test --logger "trx;LogFileName=UnitTestResults.trx" eShopOnWeb.sln'
      }
    }
    stage('Build Image') {
		steps {
			echo 'Build docker image'

			script {
				//dockerImage = docker.build registry + ":latest"
				//dockerImage = docker.build registry + ":latest", src/Web/Dockerfile.jenkins
				sh 'docker build --pull -t web' + ":${env.BUILD_TAG}" + ' -f src/Web/Dockerfile.jenkins .'
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
    stage('Publish Image') {
      steps{
        script {
			withDockerRegistry(credentialsId: 'DockerHub', url: '') {
				echo 'docker tag web' + ':${env.BUILD_TAG} ' + 'sankalpreddy/eshoponweb' + ':${env.BUILD_TAG}'
				sh 'docker tag web' + ':${env.BUILD_TAG} ' + 'sankalpreddy/eshoponweb' + ':${env.BUILD_TAG}'
				//sh 'docker push sankalpreddy/eshoponweb' + ':${env.BUILD_TAG}'
			}
          //docker.withRegistry( '', registryCredential ) {
            //dockerImage.push()
          //}
        }
      }
    }
    //stage('Remove Unused docker image') {
      //steps{
        //sh "docker rmi $registry:latest"
      //}
    //}
  }
}