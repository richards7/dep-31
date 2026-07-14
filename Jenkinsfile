pipeline {
    agent { label 'docker-agent' }
    environment {
        IMAGE_NAME = "my-app"
        REGISTRY = "testingacountwork"
        FULL_IMAGE = "${REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}"
    }
    stages {
        stage('Build') {
            steps {
                sh "docker build -t ${FULL_IMAGE} ."
            }
        }
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-credentials-id') {
                        sh "docker push ${FULL_IMAGE}"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Replace placeholder with actual image tag
                    sh "sed -i 's|DOCKER_IMAGE_PLACEHOLDER|${FULL_IMAGE}|g' k8s/deployment.yaml"
                    
                    withCredentials([file(credentialsId: 'minikube-kubeconfig', variable: 'KUBECONFIG')]) {
                        sh "kubectl apply -f k8s/deployment.yaml"
                        sh "kubectl apply -f k8s/service.yaml"
                    }
                }
            }
        }
    }
}
