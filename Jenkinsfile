pipeline {
    agent any

    environment {
        IMAGE_NAME = "gp97dot/capstone2-app"
        K8S_MASTER = "ubuntu@3.110.186.8"
        SSH_KEY = "/var/lib/jenkins/.ssh/capstone"
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'master', url: 'https://github.com/Gurvak-Singh/website.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ${K8S_MASTER} '
                    cd ~/website &&
                    git pull origin master &&
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
                """
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ${K8S_MASTER} '
                    docker push ${IMAGE_NAME}:${BUILD_NUMBER} &&
                    docker logout'
                """
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ${K8S_MASTER} '
                    kubectl apply -f ~/website/k8s-deployment.yaml &&
                    kubectl set image deployment/capstone2-app capstone2-app=${IMAGE_NAME}:${BUILD_NUMBER}'
                """
            }
        }
    }
}
