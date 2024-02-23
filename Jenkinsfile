pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-id', url: 'https://github.com/Daudkhan1/jenkins-on-kubernetes-pipeline.git']]])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t Daudkhan1/nodejsapp .'
                }
            }
        }
        stage('Deploy Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'docker-id', variable: 'docker-id')]) {
                        sh 'docker login -u Daudkhan1 -p ${docker-id}'
                    }
                    sh 'docker push devopshint/nodejsapp-1.0:latest'
                }
            }
        }
        stage('Deploy App on k8s') {
            steps {
                sshagent(['k8s']) {
                    sh "scp -o StrictHostKeyChecking=no nodejsapp.yaml ubuntu@IPofk8scluster:/home/ubuntu"
                    script {
                        try {
                            sh "ssh ubuntu@IPofk8scluster kubectl create -f ."
                        } catch(error) {
                            sh "ssh ubuntu@IPofk8scluster kubectl create -f ."
                        }
                    }
                }
            }
        }
    }
}

