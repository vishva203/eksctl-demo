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
        stage('Delete EKS Cluster') {
            steps {
			withAWS(credentials: 'aws-eks-creds', region: "${AWS_DEFAULT_REGION}") {
			    sh '''
                    sh "eksctl delete cluster --name ${CLUSTER_NAME} --region ${AWS_DEFAULT_REGION}"
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
