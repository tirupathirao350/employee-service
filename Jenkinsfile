pipeline {
  agent any
  tools { 
        maven 'Maven'
        jdk 'JAVA_HOME'
  }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
    stage('Docker Build') {
      steps {
        sh '/usr/bin/docker build -t satheeshch/employee-service:latest .'
      }
    }
    stage('Push image') {
      steps {
        withDockerRegistry([credentialsId: 'docker-hub', url: "https://index.docker.io/v1/"]) {
          sh '/usr/bin/docker push satheeshch/employee-service:latest'
        }
      }
    }
    stage('deploy to ECR') {
      steps {
        node('EKS-master'){
          checkout scm
         sh 'kubectl apply -f deployment.yaml' 
         sh 'kubectl apply -f service.yaml' 
        }
      }
    }
  }
}
