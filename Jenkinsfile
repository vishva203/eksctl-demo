pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "eu-west-2"
        CLUSTER_NAME = "demo-eks-cluster"
        // This automatically sets AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
        // if Jenkins credentials are configured as type "AWS Credentials"
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
                            --node-type t3.small \
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
                    echo "Updating kubeconfig..."
                    aws eks update-kubeconfig --region ${AWS_DEFAULT_REGION} --name ${CLUSTER_NAME}
                    echo "Verifying cluster connectivity..."
                    kubectl get nodes
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                echo "Applying Kubernetes manifests..."
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

