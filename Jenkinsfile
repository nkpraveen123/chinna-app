
pipeline {
    agent any

    stages {
        
        stage('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build . -t bussar/hiring:${commit_id()}"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker', variable: 'cred')]) {
                    sh "docker login -u bussar -p ${cred}"
                    sh "docker push bussar/hiring:${commit_id()}"
                }
            }
        }
        stage('Docker Deploy') {
            steps {
                sshagent(['docker-host']) {
                    sh "ssh -o StrictHostKeyChecking=no  ec2-user@172.31.36.37 docker rm -f hiring"
                    sh "ssh  ec2-user@172.31.36.37 docker run -d -p 8080:8080 --name hiring bussar/hiring:${commit_id()}"
                }
            }
        }

    }
}

def commit_id(){
    id = sh returnStdout: true, script: 'git rev-parse HEAD'
    return id
}
