pipeline {
    agent {
        label "ec2"
    }

    stages {
        stage('Download the source code') {
            steps {
                git branch: 'main', url: 'https://github.com/sudhanshuvlog/SnakeGame.git'
                echo "Code downloaded successfully"
            }
        }

        stage('Test') {
            steps {
                sh "yum install python3-pip-21.3.1-2.amzn2023.0.5.noarch -y"
                sh "pip install -r requirements.txt"
                sh "pytest"
                echo "Code has been tested successfully!"
            }
        }

        stage("Build Docker Image") {
            steps {
                sh "docker build -t gfgwebimg ."
            }
        }

        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                    sh "docker tag gfgwebimg \$DOCKER_USER/gfgwebimg:latest"
                    sh "docker push \$DOCKER_USER/gfgwebimg:latest"
                    echo "Docker image pushed to Docker Hub successfully!"
                }
            }
        }

        stage("Deployment") {
            steps {
                sh "docker rm -f webos || true"
                sh "docker run -dit --name webos -p 5000:5000 gfgwebimg"
            }
        }
    }
}
