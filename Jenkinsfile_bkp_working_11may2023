pipeline {
  agent { label 'ec2-ubuntu-slave' }
  
  environment{
    DOCKER_USERNAME = credentials('DOCKER_USERNAME')
    DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
  }
  stages {
    stage('Fetch') {
      steps {
        git(branch: 'main', url: 'https://github.com/anooprs471/phppot-event-demo-01.git')
      }
    }

    stage('docker login') {
		steps{
			sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
		}
    }

    stage('Build-Image') {
      steps {
        sh "pack build anooprs471/phppot-event:${BUILD_NUMBER} --path . --buildpack paketo-buildpacks/php --builder paketobuildpacks/builder:full"}
    }
	
    stage('docker push') {
		steps{
			sh "docker push anooprs471/phppot-event:${BUILD_NUMBER}"
		}
    }

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
