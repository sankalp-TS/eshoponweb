def dockerRegistry = "sankalpreddy/eshoponweb"
def shRetVal = ""

pipeline {
	environment {
		//registry = "varma03/node-frontend"
		//registryCredential = 'dockerhub'
		dockerImage = ''
	}
	
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
				// Create 2 tags
				// 1. Tag image with the docker registry name and an unique build tab (BUILD_TAG)
				// 2. Tag image with the docker registry name and a static tag (latest)
				sh 'docker tag web' + ":${env.BUILD_TAG} " + "${dockerRegistry}:${env.BUILD_TAG}"
				sh 'docker tag web' + ":${env.BUILD_TAG} " + "${dockerRegistry}:latest"
				
				// Push the image with unique build tag to the registry (hub.docker.com)
				shRetVal = sh(
					scritp: "docker push ${dockerRegistry}" + ":${env.BUILD_TAG}",
					returnStdout: true).trim()
				echo "${shRetVal}"
				// Push the image with the static tag "latest" only after deleting the existing one in the registry
				sh "docker push ${dockerRegistry}" + ":latest"
			}
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