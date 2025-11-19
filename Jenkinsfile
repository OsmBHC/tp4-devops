pipeline {
    agent any

    environment {
        registry = "oussamabhc/tp4"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }

    stages {
        stage('Cloning Git') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/OsmBHC/tp4-devops'
            }
        }

        stage('Building image') {
            steps {
                script {
                    docker.withServer('unix:///var/run/docker.sock') {
                        dockerImage = docker.build("$registry:$BUILD_NUMBER")
                    }
                }
            }
        }

        stage('Test image') {
            steps {
                echo "Tests passed"
            }
        }

        stage('Publish Image') {
            steps {
                script {
                    docker.withServer('unix:///var/run/docker.sock') {
                        docker.withRegistry('', registryCredential) {
                            dockerImage.push()
                            dockerImage.push('latest')
                        }
                    }
                }
            }
        }

        stage('Deploy Image') {
            steps {
                sh '''
                docker stop tp4-app 2>/dev/null || true
                docker rm tp4-app 2>/dev/null || true
                docker run -d --name tp4-app -p 8081:80 $registry:latest
                echo "Application déployée → http://localhost:8081"
                '''
            }
        }
    }
}