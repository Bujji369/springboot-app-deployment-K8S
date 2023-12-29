def eksCluster="my-eks-cluster"
def region="ap-south-1"
def artifactName = "${env.BUILD_NUMBER}"
pipeline {
    tools {
        maven 'Maven3'
    }
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Bujji369/springboot-app-deployment-K8S.git']]])
            }
        }
        stage('Build Jar') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Docker Image Build') {
            steps {
                sh "docker build -t srirammani/sriram369:mani-ram-${artifactName} ."
        }
        stage('image upload DockerHub') {
            steps {
			        script {
			   
                  withCredentials([string(credentialsId: 'DOCKER-HUB-CREDENTIALS', variable: 'DOCKERHUB')]) {
            
                    sh "docker login -u srirammani -p ${DOCKERHUB}"
                  }
          
                  sh "docker push srirammani/sriram369:mani-ram-${artifactName}"
			   
			        }
            }
        }
        stage('Integrate Jenkins with EKS Cluster and Deploy App') {
            steps {
                withAWS(credentials: '<AWS_CREDENTIALS_ID>', region: '<AWS_REGION>') {
                  script {
                    sh ('aws eks update-kubeconfig --name <EKS_CLUSTER_NAME> --region <AWS_REGION>')
                    sh "kubectl apply -f <K8S_DEPLOY_FILE>.yaml"
                }
                }
        }
    }
    }
}
