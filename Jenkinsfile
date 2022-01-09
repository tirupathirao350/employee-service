pipeline {
  agent any
  tools { 
        maven 'Maven'
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
        sh '/usr/bin/docker build -t employee-service .'
      }
    }   
    stage('push image to ECR'){
      steps {
        withDockerRegistry(credentialsId: 'ecr:us-west-2:aws-credentials', url: 'public.ecr.aws/e5n9b6i1/emplyee-service') {
          sh 'docker tag employee-service:latest public.ecr.aws/e5n9b6i1/emplyee-service:latest'
         sh 'docker push public.ecr.aws/e5n9b6i1/emplyee-service:latest'
        } 
      }
    }
    stage('deploy to ECR') {
      steps {
         sh 'kubectl apply -f deployment.yaml' 
         sh 'kubectl apply -f service.yaml' 
        
      }
    }
  }
}
