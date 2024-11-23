pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'trong19/golangserver'
        DOCKER_TAG = 'golang'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/TrongPham1920/derverdolang.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}", "-f Dockerfile .")
                }
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy Golang to DEV') {
            steps {
                echo 'Deploying to DEV...'
                sh 'docker image pull trong19/golangserver:golang'
                
                // Dừng và xóa container cũ nếu tồn tại
                sh 'docker container stop server-golang || echo "container does not exist"'
                sh 'docker container rm server-golang || echo "container does not exist"'

                // Tạo lại container mới
                sh 'docker network create dev || echo "this network exists"'
                sh 'docker container prune -f'
                sh 'docker container run -d --rm --name server-golang -p 4000:3000 --network dev trong19/golangserver:golang'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
