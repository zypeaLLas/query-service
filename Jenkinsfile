pipeline {

  environment {
    dockerimagename = "killua214/query-service"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git credentialsId: 'github-credentials',
			url: 'https://github.com/zypeaLLas/query-service.git',
            branch: 'main'  // Đảm bảo tên branch đúng
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }
	
	stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
			withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
                        // Sử dụng withEnv để tránh String interpolation
                        withEnv(["KUBECONFIG=$KUBECONFIG"]) {
                            sh 'kubectl apply -f deployment.yaml -f service.yaml'
                        }
                    }
        }
      }
    }
    

  }

}
