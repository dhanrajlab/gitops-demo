pipeline {
    agent any
    environment {
        DOCKERHUB_USERNAME = "ndhanraj"
        APP_NAME = "gitops-demo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'docker' 
    }
    stages {
	stage('Cleanup Workspace'){
            steps {
                script {
                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){
            steps {
                git credentialsId: 'github', 
                url: 'https://github.com/dhanrajlab/gitops-demo.git',
                branch: 'master'
            }
        }
        stage('Build Docker Image'){
            steps {
                script{ 
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker Image'){
            steps {
                script{
                    docker.withRegistry('', REGISTRY_CREDS ){
                        docker_image.push("${BUILD_NUMBER}")
                        docker_image.push('latest')
                    }
                }
            }
        } 
        stage('Delete Docker Images'){
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker rmi ${IMAGE_NAME}:latest"
            }
         }
        stage ('Trigger Repo Pipeline'){
		steps {
			sh "curl -v -k --user admin:11d8530504ef751c85d03efb0d9c540407 -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://34.171.77.47:8080/job/gitops-config/buildWithParameters?token=gitops-config'"
			}
		}
	} 
}
