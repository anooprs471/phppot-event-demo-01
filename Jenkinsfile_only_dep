pipeline {
  agent { label 'ec2-ubuntu-slave' }
  
  stages {


    stage('Deploy App to Kubernetes') {     
      steps {
          withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
            sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" phppot-event.yaml'
            sh 'kubectl apply -f config-map.yaml'
	          sh 'kubectl apply -f phppot-event.yaml'
	          sh 'kubectl apply -f phppot-event-ingress.yaml'
          }
      }
    }

  }
}
