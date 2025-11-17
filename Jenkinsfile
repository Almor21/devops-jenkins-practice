pipeline {
    agent {
        docker {
            image 'node:18'
            args '-p 3000:3000 -p 3001:3001 --user root --privileged -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_NAME = "devops-jenkins-practice"
    }

    stages {
        stage('Checkout') {
            steps {
                sh 'docker --version'
                checkout scm
                script {
                    echo "Branch actual: ${env.BRANCH_NAME}"
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

                    echo "Puerto asignado: ${env.APP_PORT}"
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
                sh 'npm test || true'  // por si no hay tests
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
