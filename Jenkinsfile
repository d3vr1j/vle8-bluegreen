pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t myapp:v2 .'
      }
    }
    stage('Load into Minikube') {
      steps {
        sh 'docker save myapp:v2 | docker exec -i minikube docker load'
      }
    }
    stage('Deploy Green') {
      steps {
        sh 'kubectl apply -f deployment-green.yaml'
        sh 'kubectl rollout status deployment/myapp-green --timeout=120s'
      }
    }
    stage('Switch Traffic') {
      steps {
        input "Switch to Green?"
        sh '''
          kubectl patch service myapp-service -p '{"spec":{"selector":{"app":"myapp","color":"green"}}}'
          kubectl describe service myapp-service | grep Selector
        '''
      }
    }
  }
}
