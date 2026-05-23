pipeline {
    agent any

    environment {
        JAVA_HOME  = tool name: 'Java'
        MAVEN_HOME = tool name: 'Maven'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Debopriyo6/Application-deployment.git'
            }
        }

        stage('Build backend') {
            steps {
                dir('backend-java') {
                    withEnv(["PATH+MAVEN=$MAVEN_HOME/bin"]) {
                        sh 'mvn clean install -DskipTests'
                    }
                }
            }
        }

        stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub_creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Docker build & push') {
            steps {
                script {
                    def imageName = "debopriyoray662001824/backend-application"
                    def imageTag  = "latest"

                    sh """
                        docker build -t ${imageName}:${imageTag} backend-java/
                        docker push ${imageName}:${imageTag}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Backend built and Docker image pushed to DockerHub!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
