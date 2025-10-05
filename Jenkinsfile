pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "eu-west-2"
        CLUSTER_NAME = "demo-eks-cluster"
        AWS_CREDS = credentials('aws-eks-creds')
    }

    stages {
        stage('Delete EKS Cluster') {
            steps {
                // Export AWS credentials for eksctl
                sh '''
                export AWS_ACCESS_KEY_ID=${AWS_CREDS_USR}
                export AWS_SECRET_ACCESS_KEY=${AWS_CREDS_PSW}
                eksctl delete cluster --name ${CLUSTER_NAME} --region ${AWS_DEFAULT_REGION}
                '''
            }
        }
    }

    post {
        success {
            echo "✅ EKS cluster deleted successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Please check the logs."
        }
    }
}

