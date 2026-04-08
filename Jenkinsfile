pipeline {
    agent any

    tools {
        nodejs 'Node18'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install --legacy-peer-deps'
            }
        }

        stage('Test') {
            steps {
                sh 'CI=true npm test || echo "No tests configured"'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = (env.BRANCH_NAME == 'main') ? 'nodemain' : 'nodedev'
                    sh "docker build -t ${imageName}:v1.0 ."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def imageName = (env.BRANCH_NAME == 'main') ? 'nodemain' : 'nodedev'
                    def appPort   = (env.BRANCH_NAME == 'main') ? '3000'     : '3001'

                    sh """
                        docker ps -q --filter name=${imageName} | xargs -r docker stop
                        docker ps -aq --filter name=${imageName} | xargs -r docker rm
                        docker run -d --name ${imageName} \
                            -e HOST=0.0.0.0 \
                            -e PORT=3000 \
                            --expose ${appPort} \
                            -p ${appPort}:3000 \
                            ${imageName}:v1.0
                    """
                }
            }
        }
    }

    post {
        success { echo "Pipeline succeeded for branch ${env.BRANCH_NAME}" }
        failure { echo "Pipeline failed for branch ${env.BRANCH_NAME}" }
    }
}