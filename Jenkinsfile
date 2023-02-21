pipeline {
    agent any

    tools{
        maven 'maven'
    }
    environment {
        registry = "005763660661.dkr.ecr.ap-south-1.amazonaws.com/uc5-ecr"
    }
    stages {
        stage('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/akshugithub/UC5-Deploy-to-EKS-Cluster.git']])
            }
        }
        stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
        }
        stage('build image') {
        steps{
        script {
          docker.build registry 
        }
      }
    }
       stage('Push image to ECR') {
       steps{  
         script {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 005763660661.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker push 005763660661.dkr.ecr.ap-south-1.amazonaws.com/uc5-ecr:latest'
         }
        }
	}
      stage('eks deploy'){
      steps{
            withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'uc6-ansible', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
              sh 'kubectl apply -f eks-deploy-k8s.yaml'
              sh 'kubectl rollout restart deployment springboot-app'
          }
      }
	}    
    
    }
}
