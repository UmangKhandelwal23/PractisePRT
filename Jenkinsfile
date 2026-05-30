pipeline {
   agent { label 'Slave01' }
 
   environment {
        DOCKERHUB_CREDENTIALS = credentials('docker')
        IMAGE_NAME = "umangkhandelwal/practicerprt:v1"
        K8_NAMESPACE = "employee-portal"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/UmangKhandelwal23/AprilPRT'
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME} .
                """
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                docker push ${IMAGE_NAME}
                """
            }
        }

        stage('Create Namespace') {
            steps {
                sh """
                kubectl get namespace ${K8_NAMESPACE} || kubectl create namespace ${K8_NAMESPACE}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl apply -f k8/ -n ${K8_NAMESPACE}
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                kubectl get pods -n ${K8_NAMESPACE}
                kubectl get svc -n ${K8_NAMESPACE}
                """
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀 Employee Portal is live"
        }
        failure {
            echo "Pipeline Failed ❌ Check logs"
        }
    }
}
