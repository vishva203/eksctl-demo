pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "eu-west-2"
        CLUSTER_NAME = "demo-eks-cluster"
        // Automatically sets AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
        AWS_CREDS = credentials('aws-eks-creds')
    }

    stages {
        stage('Delete EKS Cluster') {
            steps {
                withAWS(credentials: "${AWS_CREDS}", region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        eksctl delete cluster --name ${CLUSTER_NAME} --region ${AWS_DEFAULT_REGION}
                    '''
                }
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
