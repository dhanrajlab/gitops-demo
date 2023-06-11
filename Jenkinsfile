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
			sh "curl -v -k --user admin:11251923eb78dddf680ec80b0d60c3d856 -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://35.193.128.97:8080/job/gitops-config/buildWithParameters?token=gitops-config'"
			}
		}
	} 
}
