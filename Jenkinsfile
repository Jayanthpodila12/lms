pipeline {
    agent any
    environment {
        REGISTRY_CREDENTIALS = "dockerhub"
        AWS_CREDENTIALS = 'awscredentials'
        AWS_REGION = "us-east-1"
        KUBECONFIG_CREDENTIALS = 'kubeconfig'
    }
    stages {
        stage('Extract Version') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    env.APP_VERSION = packageJson.version
                    echo "App Version: ${APP_VERSION}"
                }
            }
        }
        stage('Build and Push Docker Images and deploy in containers') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: REGISTRY_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        echo $DOCKER_PASS | sudo docker login -u $DOCKER_USER --password-stdin
                        sudo docker build -t jayanthpodila/lms:${APP_VERSION} webapp/
                        sudo docker push jayanthpodila/lms:${APP_VERSION}
                        """
                    }
                }
            }
        }
       stage('Authenticate with AWS and EKS') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'awscredentials']]) {
                    sh '''
                        aws eks update-kubeconfig --region $AWS_REGION --name lms
                    '''
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_CREDENTIALS')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG_CREDENTIALS
                        echo "Using KUBECONFIG: \$KUBECONFIG_CREDENTIALS"
                        sed -i "s|IMAGE_VERSION|${APP_VERSION}|g" deployment.yml
                        kubectl apply -f deployment.yml
                    '''
                }
            }
        }
    }
}    

                         
   
 
