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
        withDockerRegistry(credentialsId: 'ecr:us-west-2:aws-credentials', url: 'http://037217839351.dkr.ecr.us-west-2.amazonaws.com/employee-service') {
          sh 'docker tag employee-service:latest 037217839351.dkr.ecr.us-west-2.amazonaws.com/employee-service:latest'
         sh 'docker push 037217839351.dkr.ecr.us-west-2.amazonaws.com/employee-service:latest'
        } 
      }
    }
    stage('deploy to ECR') {
      steps {
      node('eks'){    
              checkout scm
        sh 'export KUBECONFIG=~/.kube/config'
         sh 'aws eks update-kubeconfig --name eks-master --region us-west-2'
        sh 'kubectl get svc'
        }
      }
    }
  }
}
