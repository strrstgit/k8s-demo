pipeline {
  agent {
    kubernetes {
      label 'jenkins-kaniko-agent'
    }
  }

  environment {
    IMAGE_NAME = "docker.io/strrstgit/demo-app"
    TAG = "${BUILD_NUMBER}"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Push Image (Kaniko)') {
      steps {
        container('kaniko') {
          sh """
          /kaniko/executor \
            --context \$(pwd) \
            --dockerfile Dockerfile \
            --destination \$IMAGE_NAME:\$TAG \
            --destination \$IMAGE_NAME:latest
          """
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        container('kubectl') {
          sh """
          kubectl apply -f k8s/deployment.yaml
          kubectl rollout status deployment/demo-app -n default
          """
        }
      }
    }
  }
}
