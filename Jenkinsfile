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
				shRetVal = sh(
					script: "docker build --pull -t web:${env.BUILD_TAG} -f src/Web/Dockerfile.jenkins .",
					returnStatus: true)
				
				// Remove dangling images
				if (shRetVal == 0) {
					sh 'docker rmi $(docker images --filter "dangling=true" -q --no-trunc)'
				}
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
				
				// Push the image with unique build tag to the registry (hub.docker.com) and delete locally
				shRetVal = sh(
					script: "docker push ${dockerRegistry}" + ":${env.BUILD_TAG}",
					returnStatus: true)
				//.trim()
				//echo "EchoB ${shRetVal} EchoE"
				if (shRetVal == 0) {
					sh "docker rmi ${dockerRegistry}" + ":${env.BUILD_TAG}"
				}

				// Push the image with the static tag "latest". This will overwrite the existing image with the same tag.
				// Subsequently delete the images.
				shRetVal = sh(
					script: "docker push ${dockerRegistry}" + ":latest",
					returnStatus: true)
				if (shRetVal == 0) {
					shRetVal = 1
					shRetVal = sh( 
						script: "docker rmi ${dockerRegistry}" + ":latest",
						returnStatus: true)
				}

				// Delete image created by the Dockerfile
				if (shRetVal == 0) {
					sh "docker rmi web:${env.BUILD_TAG}"
				}
			}
        }
      }
    }
    stage('Deploy') {
      steps{
		script  {
			//withCredentials([sshUserPrivateKey(credentialsId: 'Technosoft.PEM', keyFileVariable: '', passphraseVariable: '', usernameVariable: '')]) {
				//script: 'ssh -i technosoft.pem ubuntu@13.232.165.181 "sudo docker run --name eshopweb --rm -i -p 80:80 sankalpreddy/eshoponweb:latest"',
			//	shRetVal = sh(
			//		script: 'ssh -o StrictHostKeyChecking=no ubuntu@13.232.165.181 "sudo docker images"',
			//		returnStdout: true)
			//	echo "EStart ${shRetVal} EEnd"
			//}

			// assume db to be running
			sshagent(credentials: ['Technosoft.PEM']){
				// To fix "Host key verification failed" one of the following fixes can be used
				// First one is: Log into your Jenkins server and manually ssh to that machine and accept the key.
				// Second one is: Add the following to your ssh command: -o StrictHostKeyChecking=no
				shRetVal = sh(
					script: 'ssh -o StrictHostKeyChecking=no ubuntu@13.232.165.181 ' + "sudo docker run --name eshopweb --rm -d -p 80:80 sankalpreddy/eshoponweb:${env.BUILD_TAG}",
					returnStdout: true)
				echo "EStart ${shRetVal} EEnd"
			}
		}
      }
    }
  }
}