pipeline {
  environment {
    registry = "captivityofpassion/cicd-k8s-demo"
    registryCredential = 'docker-hub-credentials'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Compile') {
      steps {
        git 'https://github.com/Captivityofpassion/PracticeCI-CD.git'
        script{
                def mvnHome = tool name: 'MAVEN_HOME', type: 'maven'
                bat "${mvnHome}/bin/mvn package"
        }
      }
    }
    stage('Building Docker Image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image To Docker Hub') {
      steps{
        script {
          /* Finally, we'll push the image with two tags:
                   * First, the incremental build number from Jenkins
                   * Second, the 'latest' tag.
                   * Pushing multiple tags is cheap, as all the layers are reused. */
          docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
              dockerImage.push("${env.BUILD_NUMBER}")
              dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy to Kubernetes'){
        steps{
            bat 'kubectl apply -f deployment.yml'
       }
    }
  }
}
