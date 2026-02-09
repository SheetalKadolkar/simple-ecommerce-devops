pipeline {
    agent any

    environment {
        IMAGE_NAME = "sheetalkadolkar/ecommerce-app"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        DOCKER_CREDS = "docker-hub-creds"
        KUBE_NAMESPACE = "default"
    }

    stages {

        stage("Clone Code") {
            steps {
                git branch: 'main',
                    url: 'https://github.com/SheetalKadolkar/simple-ecommerce-devops.git'
            }
        }

        stage("Docker Check") {
            steps {
                sh 'docker --version'
            }
        }

        stage("Build Docker Image") {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage("Login & Push Docker Image") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage("Deploy to EKS") {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl get nodes
                        kubectl set image deployment/ecommerce-deployment \
                        ecommerce=${IMAGE_NAME}:${IMAGE_TAG} -n ${KUBE_NAMESPACE}

                        kubectl rollout status deployment/ecommerce-deployment -n ${KUBE_NAMESPACE}
                    '''
                }
            }
        }

    }

}
