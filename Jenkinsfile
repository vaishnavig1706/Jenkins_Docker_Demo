pipeline {
    agent any

    environment {
        IMAGE_NAME = "yourdockerhubusername/jenkins-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/<your-username>/jenkins-docker-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh 'docker rm -f jenkins-demo || true'
                    sh 'docker run -d --name jenkins-demo -p 5000:5000 $IMAGE_NAME'
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    sh 'curl -f http://localhost:5000'
                }
            }
        }

        stage('Push to DockerHub') {
            when {
                expression { return env.BRANCH_NAME == 'main' }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed!'
        }
    }
}


