pipeline {
  agent { label 'ec2-ubuntu-node' }
  
  stages {
    stage('Fetch') {
      steps {
        git(branch: 'main', url: 'https://github.com/anooprs471/phppot-event-demo-01.git')
      }
    }

    stage('Build-Image') {
      steps {
        sh "pack build anooprs471/phppot-event:${BUILD_NUMBER} --path . --buildpack paketo-buildpacks/php --builder paketobuildpacks/builder:full"}
    }
	
    stage('docker push') {
		steps{
			withCredentials([usernamePassword(credentialsId: 'Dockerhub_creds_username_password', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]){
			sh "docker login -u $DOCKER_REGISTRY_USER -p $DOCKER_REGISTRY_PWD"
			sh "docker push anooprs471/phppot-event:${BUILD_NUMBER}"
			}
			}
    }

    stage('Deploy App to Kubernetes') {     
      steps {
            sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" phppot-event.yaml'
            sh 'kubectl apply -f config-map.yaml'
	    sh 'kubectl apply -f mariadb-phppot-event.yaml'
	    sh 'kubectl apply -f phppot-event.yaml'
	    sh 'kubectl apply -f phppot-event-ingress.yaml'
      }
    }
  }
}
