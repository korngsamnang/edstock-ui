pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-1'
        AWS_ACCOUNT_ID = '767398103155'
        ECR_REPO = 'edstock-ui'
        IMAGE_TAG = ''
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Set Image Tag') {
            steps {
                script {
                    IMAGE_TAG = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    withCredentials([aws(credentialsId: 'aws-credentials-id', region: "${AWS_REGION}")]) {
                        sh """
                            aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                        """
                    }
                }
            }
        }

        stage('Build and Push UI Image') {
            steps {
                script {
                    sh """
                        docker build -t ${ECR_REPO}:${IMAGE_TAG} .
                        docker tag ${ECR_REPO}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}
                        docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy UI to EKS') {
            steps {
                script {
                    withCredentials([aws(credentialsId: 'aws-credentials-id', region: "${AWS_REGION}")]) {
                        dir('k8s') {
                            sh 'aws eks --region ${AWS_REGION} update-kubeconfig --name my-eks-cluster'
                            sh 'kubectl apply -f frontend-deployment.yaml'
                            sh "kubectl set image deployment/frontend frontend=${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}"
                            sh 'kubectl apply -f frontend-service.yaml'
                        }
                    }
                }
            }
        }

    }

    post {
        success {
            echo "✅ Newly built UI has been deployed to EKS!"
        }
        failure {
            echo "❌ Failed to deploy UI to EKS!"
        }
    }
}
