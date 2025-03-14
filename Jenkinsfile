pipeline {

  environment {
    dockerimagename = "nhutlinh231/backend-k8s"
    dockerImage = ""
  }

  agent any

  tools {
    nodejs '20.13.1'
  }

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/nhutlin/Instagram-backend.git'
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
            withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: 'kubernetes-admin@kubernetes', credentialsId: 'kubeconfig', namespace: 'default', restrictKubeConfigAccess: false) {
              sh 'kubectl apply -f /var/lib/jenkins/workspace/Github-BE-Instagram/deployment.yml'
              sh 'kubectl get all'
            } 
          }
        }
      }
    }
  }
  
  // post {
  //       always {
  //           // Clean up docker images
  //           cleanWs()
  //           sh "docker image prune -f"
  //       }
  //   }

}