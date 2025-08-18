pipeline {
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = 'dockerhub'   // Jenkins Credentials ID for DockerHub
    GITHUB_CREDENTIALS = 'github'         // Jenkins Credentials ID for GitHub
    DOCKER_IMAGE = 'YOUR-DOCKERHUB-USERNAME/python-flask-app-devops'            // 👈 PASTE YOUR DOCKERHUB-USERNAME HERE
    DEPLOYMENT_MANIFEST = 'k8s/manifests/deployment.yaml'
  }

  stages {
    stage('Checkout code') {
      steps {
        git credentialsId: "${GITHUB_CREDENTIALS}", url: 'https://github.com/YOUR-GITHUB-USERNAME/python-flask-app-devops.git'     // 👈 PASTE YOUR GITHUB-USERNAME HERE
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        script {
          docker.withRegistry('', "${DOCKERHUB_CREDENTIALS}") {
            dockerImage.push("${env.BUILD_NUMBER}")
            dockerImage.push('latest')
          }
        }
      }
    }

    stage('Update Kubernetes Deployment') {
      steps {
        script {
          sh """
            sed -i 's|image:.*|image: ${DOCKER_IMAGE}:${env.BUILD_NUMBER}|' ${DEPLOYMENT_MANIFEST}
            git config user.email "example@example.com"                                               // 👈 PASTE YOUR EMAIL HERE 
            git config user.name "GITHUB-USERNAME"                                                     // 👈 PASTE YOUR GITHUB-USERNAME HERE
            git add ${DEPLOYMENT_MANIFEST}
            git commit -m "Update image tag to ${env.BUILD_NUMBER} via Jenkins"
            git push origin main
          """
        }
      }
    }
  }
}
// Note: Replace YOUR-DOCKERHUB-USERNAME and YOUR-GITHUB-USERNAME with your actual DockerHub and GitHub usernames.
// Note: Add your original email and name in the git config commands. 


