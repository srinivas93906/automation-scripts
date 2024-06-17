pipeline {
    agent any

    parameters {
        choice(name: 'BRANCH', choices: ['main', 'develop', 'feature-develop'], description: 'Select the branch to deploy')
        choice(name: 'CONTAINERS', choices: ['FRONTEND_IMAGE', 'BACKEND_IMAGE'], description: 'Select the container to deploy')
    }

    environment {
        DOCKER_REGISTRY = 'docker.io'
        FRONTEND_IMAGE = 'your-username/frontend-image-placeholder'
        BACKEND_IMAGE = 'your-username/backend-image-placeholder'
        FRONTEND_REPO = 'https://github.com/your-username/frontend-repo-placeholder.git'
        BACKEND_REPO = 'https://github.com/your-username/backend-repo-placeholder.git'
        DOCKERHUB_CREDENTIALS = credentials('docker_registry_credentials_id')
        GIT_CREDENTIALS = 'git-credentials-id'
        PROD_SERVER = 'your-prod-server-ip'
        PROD_USER = 'your-prod-server-user'
        PROD_DIR = '/home/your-prod-server-user/'
        PROD_SSH_CREDENTIALS = 'prod-ssh-credentials-id'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    if (params.CONTAINERS == 'FRONTEND_IMAGE') {
                        git branch: "${params.BRANCH}", url: "${FRONTEND_REPO}", credentialsId: "${GIT_CREDENTIALS}"
                    } else if (params.CONTAINERS == 'BACKEND_IMAGE') {
                        git branch: "${params.BRANCH}", url: "${BACKEND_REPO}", credentialsId: "${GIT_CREDENTIALS}"
                    }
                }
            }
        }

        stage('Build JAR File') {
            steps {
                script {
                    if (params.CONTAINERS == 'BACKEND_IMAGE') {
                        sh 'mvn clean install -DskipTests=true'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker_registry_credentials_id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    }

                    if (params.CONTAINERS == 'FRONTEND_IMAGE') {
                        docker.build("${FRONTEND_IMAGE}:1.0.1", "-f Dockerfile .")
                    } else if (params.CONTAINERS == 'BACKEND_IMAGE') {
                        docker.build("${BACKEND_IMAGE}:1.0.1", "-f Dockerfile .")
                    }
                }
            }
        }

        stage('Push to Registry') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'docker_registry_credentials_id',
                            usernameVariable: 'DOCKERHUB_USERNAME',
                            passwordVariable: 'DOCKERHUB_PASSWORD'
                        )
                    ]) {
                        sh "docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASSWORD}"

                        if (params.CONTAINERS == 'FRONTEND_IMAGE') {
                            docker.image("${FRONTEND_IMAGE}:1.0.1").push()
                        } else if (params.CONTAINERS == 'BACKEND_IMAGE') {
                            docker.image("${BACKEND_IMAGE}:1.0.1").push()
                        }
                    }
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                script {
                    sshagent([PROD_SSH_CREDENTIALS]) {
                        if (params.CONTAINERS == 'FRONTEND_IMAGE') {
                            def frontendImage = "${FRONTEND_IMAGE}:1.0.1"
                            sh """
                            ssh -o StrictHostKeyChecking=no ${PROD_USER}@${PROD_SERVER} <<EOF
                                docker pull ${frontendImage}
                                docker stop frontend-container-placeholder || true
                                docker rm frontend-container-placeholder || true
                                docker run -d --name frontend-container-placeholder -p 8090:3002 ${frontendImage}
EOF
                            """
                        } else if (params.CONTAINERS == 'BACKEND_IMAGE') {
                            def backendImage = "${BACKEND_IMAGE}:1.0.1"
                            sh """
                            ssh -o StrictHostKeyChecking=no ${PROD_USER}@${PROD_SERVER} <<EOF
                                docker pull ${backendImage}
                                docker stop backend-container-placeholder || true
                                docker rm backend-container-placeholder || true
                                docker run -d --name backend-container-placeholder -p 81:81 ${backendImage}
EOF
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

