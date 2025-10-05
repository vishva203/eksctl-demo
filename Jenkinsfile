pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "eu-west-2"
        CLUSTER_NAME = "demo-eks-cluster"
        AWS_CREDS = credentials('aws-eks-creds')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vishva203/eksctl-demo.git'
            }
        }

        stage('Create EKS Cluster') {
            steps {
                withAWS(credentials: 'aws-eks-creds', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                    if eksctl get cluster --name ${CLUSTER_NAME} >/dev/null 2>&1; then
                        echo "EKS cluster ${CLUSTER_NAME} already exists."
                    else
                        echo "Creating EKS Cluster..."
                        eksctl create cluster \
                            --name ${CLUSTER_NAME} \
                            --region ${AWS_DEFAULT_REGION} \
                            --nodes 2 \
                            --node-type t3.medium \
                            --managed
                    fi
                    '''
                }
            }
        }

        stage('Update kubeconfig') {
            steps {
                withAWS(credentials: 'aws-eks-creds', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                    aws eks update-kubeconfig --region ${AWS_DEFAULT_REGION} --name ${CLUSTER_NAME}
                    kubectl get nodes
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                kubectl apply -f volume.yaml
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml

                echo "Verifying Deployment..."
                kubectl get pods -o wide
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo "✅ EKS cluster created and application deployed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Please check the logs."
        }
    }
}

