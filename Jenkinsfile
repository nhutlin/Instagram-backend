pipeline {

  environment {
    dockerimagename = "nhutlinh231/backend-k8s"
    dockerImage = ""
  }

  agent any

  tools {
    nodejs '21.7.1'
  }

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/nhutlin/Instagram-backend.git'
      }
    }

    stage('Build') {
      steps {
        sh 'npm version'
        sh 'cd /var/jenkins_home/workspace/BE-Instagram-CICD && npm install'
        echo 'Install npm successfully...'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    // stage('Deploying App to Kubernetes') {
    //   steps {
    //     script {
    //       kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "kubernetes")
    //     }
    //   }
    // }
  }
  
  post {
        always {
            // Clean up docker images
            cleanWs()
            sh "docker image prune -f"
        }
    }

}