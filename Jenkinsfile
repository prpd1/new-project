pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "qwerty703"
        IMAGE_NAME = "${DOCKERHUB_USER}/simple-python-app"
        IMAGE_TAG  = "latest"
        KUBE_DEPLOYMENT_FILE = "deployment.yaml"
         DOCKER_SECRET_FILE = "secret.yaml"
    }

    stages {
        stage('Praveena - Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh '''
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Praveena - Login to Dockerhub') {
            steps {
                echo "Logging in to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Praveena - Push image to Dockerhub') {
            steps {
                echo "Pushing image to Docker Hub..."
                sh '''
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    
        stage('Praveena - Apply Docker Secret') {
            steps {
                echo "Creating Docker pull secret in Kubernetes..."
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG_FILE
                        kubectl apply -f ${DOCKER_SECRET_FILE}
                    '''
                }
            }
        }


        stage('Praveena - Deploy to Kubernetes') {
            steps {
                echo "Deploying to Kubernetes..."
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG_FILE
                        kubectl apply -f ${KUBE_DEPLOYMENT_FILE}
                        kubectl rollout status deployment/simple-python-app
                    '''
                }
            }
        }
    }

}