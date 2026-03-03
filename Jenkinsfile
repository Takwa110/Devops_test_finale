pipeline {
    agent any

    stages {

        stage('Checkout GIT') {
            steps {
                echo 'Pulling...'
                git branch: 'main',
                    url: 'https://github.com/Takwa110/Devops_test_finale.git'
            }
        }

        stage('Testing maven') {
            steps {
                sh 'mvn -version'
            }
        }

        stage('Docker Build') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker build -t $DOCKER_USER/spring-k8s-app:2.0 ./timesheet-devops
                        docker push $DOCKER_USER/spring-k8s-app:2.0
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                        echo "Using kubeconfig at $KUBECONFIG"
                        kubectl get nodes
                        kubectl apply -f k8s/deployment.yaml
                    '''
                }
            }
        }

    }
}
