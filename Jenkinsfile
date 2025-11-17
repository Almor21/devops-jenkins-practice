pipeline {
    agent {
        docker {
            image 'node-docker-agent' // Custom image with node and docker cli installed
            args '--user root --privileged -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_NAME = "devops-jenkins-practice"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                script {
                    echo "Actual Branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Set port') {
            steps {
                script {
                    if (env.BRANCH_NAME == "main") {
                        env.APP_PORT = "3000"
                    } else if (env.BRANCH_NAME == "dev") {
                        env.APP_PORT = "3001"
                    } else {
                        env.APP_PORT = "4000"
                    }

                    echo "Port: ${env.APP_PORT}"
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Build Docker image') {
            steps {
                sh """
                docker build -t ${APP_NAME}:${env.BRANCH_NAME} .
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                docker rm -f ${APP_NAME}_${env.BRANCH_NAME} || true
                docker run -d --name ${APP_NAME}_${env.BRANCH_NAME} \
                    -p ${APP_PORT}:3000 \
                    ${APP_NAME}:${env.BRANCH_NAME}
                """
            }
        }
    }
}
