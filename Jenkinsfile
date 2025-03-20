pipeline {
    agent any
    
    stages {
        stage('Git Hub') {
            steps {
                
                git branch: 'Dev', changelog: false, poll: false, url: 'https://github.com/Jayanthpodila12/lms.git'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'cd api && sudo docker run --rm -e SONAR_HOST_URL="http:/54.151.100.229:9000/" -v ".:/usr/src" -e SONAR_TOKEN="squ_997b5e890b817be4a96636112247e9d87334d78f" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms-demo'
                sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http:/54.151.100.229:9000/" -v ".:/usr/src" -e SONAR_TOKEN="squ_997b5e890b817be4a96636112247e9d87334d78f" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms-demo'
            }
        } 
        stage('DB') {
            steps {
                echo 'Database..'
                sh 'sudo docker container run -dt --name lms-db -e POSTGRES_PASSWORD=lms123 postgres'

            }
        }
        stage('Build API') {
            steps {
                echo 'Building backend.'
                sh 'cd api && sudo docker build -t myapi:v2 .'
                sh 'sudo docker container run -dt --name backend -p 8081:80 myapi:v2'
            }
        }  
        stage('Build Webapp') {
            steps {
                echo 'Building frontend.'
                sh 'cd webapp && sudo docker build -t mywebapp1:v3 .'
                sh 'sudo docker container run -dt --name frontend -p 80:80 mywebapp1:v3'
            }
        }  
        stage('docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'cend', usernameVariable: 'usr')]) {
                   sh 'sudo docker login -u ${usr} -p ${cend}' 
                   sh 'sudo docker tag myapi:v1 jayanthpodila/lms-repo:v1'
                   sh 'sudo docker push jayanthpodila/lms-repo:v1'  
               }
            }      
                         
   }
 }
}


