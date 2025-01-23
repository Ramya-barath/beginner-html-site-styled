pipeline {
    agent any    
    environment {        
        DOCKERHUB_CREDENTIALS_ID = 'c1101c3b-e018-40ab-a70a-89e62bdd3bdc'
        DOCKER_IMAGE = '14ramya91/proj_build'
        TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
        KUBECONFIG = '/etc/Kubernetes/admin.config'
        DEPLOYMENT_FILE = '/home/ubuntu/beginner-html-site-styled/deployment.yaml'
        NODEPORT_SERVICE_FILE = '/home/ubuntu/beginner-html-site-styled/service.yaml'
    }
    
    triggers {        
        githubPush()
    }

    stages {
        stage('Clone Repository') {	
            steps {
                git 'https://github.com/Ramya-barath/beginner-html-site-styled.git'
		}
         }
        
        stage('Build Docker Image') {
	   steps {						
                script {
                    // Build Docker image using Dockerfile in the repository
                    docker.build("${IMAGE_NAME}:${TAG}")
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
		    withDockerRegistry([credentialsId: 'c1101c3b-e018-40ab-a70a-89e62bdd3bdc', url: 'https://index.docker.io/v1/']) {	
		    echo 'Successfully logged into DockerHub.'
                    }
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
		    withDockerRegistry([credentialsId: 'c1101c3b-e018-40ab-a70a-89e62bdd3bdc', url: 'https://index.docker.io/v1/']) {                  
                    docker.push("${IMAGE_NAME}:${TAG}")
		   }	
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                   
                    sh """
                    kubectl apply -f ${DEPLOYMENT_FILE} 
                    kubectl apply -f ${NODEPORT_SERVICE_FILE}
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {                    
                    sh "kubectl get pods"
                    sh "kubectl get svc"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment and image push completed successfully.'
        }

        failure {
            echo 'The pipeline failed.'
        }
    }
}
