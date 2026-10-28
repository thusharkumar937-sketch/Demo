pipeline {
  agent any
  triggers {
    githubPush()
    pollSCM('H/2 * * * *')
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build Image') {
      steps { bat 'docker build -t hello-k8s:%BUILD_NUMBER% .' }
    }
    stage('Deploy') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
          bat 'kubectl apply -f k8s/app-deployment.yaml'
          bat 'kubectl apply -f k8s/app-service.yaml'
          bat 'kubectl set image deployment/hello-app web=hello-k8s:%BUILD_NUMBER%'
          bat 'kubectl rollout status deployment/hello-app --timeout=120s'
        }
      }
    }
  }
}