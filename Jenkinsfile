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
                sh 'sudo docker container run -dt --name lms-db1 -e POSTGRES_PASSWORD=lms123 postgres'

            }
        }
        stage('docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'cend', usernameVariable: 'usr')]) {
                   sh 'sudo docker login -u ${usr} -p ${cend}' 
                   
               }
            }
        }
        stage('Build API') {
            steps {
                echo 'Building backend.'
                sh 'cd api && sudo docker build -t jayanthpodila/lms-be:v1 .'
                sh 'sudo docker container run -dt --name backend -p 8081:8080 jayanthpodila/lms-be:v1'
                sh 'sudo docker push jayanthpodila/lms-be:v1'
            }
        }  
        stage('Build Webapp') {
            steps {
                echo 'Building frontend.'
                sh 'cd webapp && sudo docker build -t jayanthpodila/lms-fe:v1 .'
                sh 'sudo docker container run -dt --name frontend -p 80:80 jayanthpodila/lms-fe:v1'
                sh 'sudo docker push jayanthpodila/lms-fe:v1'
            }
        }  
              
                         
   
 }
}