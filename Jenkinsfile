pipeline {
  agent any

  environment {
    IMAGE = "sheetalkadolkar/ecommerce-app"
  }

  stages {

    stage('Clone') {
      steps {
        git 'https://github.com/SheetalKadolkar/simple-ecommerce-devops.git'
      }
    }

    stage('Build') {
      steps {
        sh 'docker build -t $IMAGE:$BUILD_NUMBER .'
      }
    }

    stage('Push') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'USER',
          passwordVariable: 'PASS')]) {

          sh 'docker login -u $USER -p $PASS'
          sh 'docker push $IMAGE:$BUILD_NUMBER'
        }
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh '''
        kubectl set image deployment/ecommerce-deployment \
        ecommerce=$IMAGE:$BUILD_NUMBER
        '''
      }
    }

  }
}
