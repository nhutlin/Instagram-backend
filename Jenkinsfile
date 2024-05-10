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
        sh 'cd /var/lib/jenkins/workspace/BE-Instagram-CICD && npm install'
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

    stage('Deploy to K8s'){
      steps{
        script{
          dir('Kubernetes') {
            withKubeConfig(caCertificate: '', clusterName: 'minikube', contextName: 'minikube', credentialsId: 'minikubeconfig', namespace: 'default', restrictKubeConfigAccess: false, serverUrl: 'https://192.168.49.2:8443/') {
              sh 'minikube start'
              sh 'kubectl apply -f /var/lib/jenkins/workspace/BE-Instagram-CICD/deployment.yml'
              sh 'kubectl get all'
            } 
          }
        }
      }
    }
  }
  
  post {
        always {
            // Clean up docker images
            cleanWs()
            sh "docker image prune -f"
        }
    }

}