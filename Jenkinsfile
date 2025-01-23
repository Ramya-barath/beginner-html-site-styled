pipeline {
    agent { label 'k8s_master'}
    environment {
        DOCKER_IMAGE = '14ramya91/beginner-html-site-styled'
        DOCKER_CREDENTIALS_ID = 'c1101c3b-e018-40ab-a70a-89e62bdd3bdc'
        KUBE_CONFIG = '/etc/kubernetes/admin.conf'
	DOCKER_PASSWORD = 'Zox29@#@@'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git (url:'https://github.com/Ramya-barath/beginner-html-site-styled.git', credentialsId:'Ramya-barath', branch: 'gh-pages')          
		 }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'echo Zox29@#@@ | sudo -S docker build -t $DOCKER_IMAGE .'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE}").push()
                       }
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml -f service.yaml --kubeconfig=$KUBE_CONFIG'
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
