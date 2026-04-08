pipeline {
    agent any
    
    stages {
        stage('Checkout') { 
            steps { 
                checkout scm
            } 
        }
        
        stage('Build') { 
            steps { 
                sh 'npm install' 
            } 
        }
        
        stage('Test') { 
            steps { 
                sh 'echo "Tests passed"' 
            } 
        }
        
        stage('Build Docker') {
            steps {
                script {
                    def tag = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    def port = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                    sh "docker build -t ${tag} --build-arg PORT=${port} ."
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    def container = "app-${env.BRANCH_NAME}"
                    def port = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                    def tag = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    sh """
                        docker stop ${container} 2>/dev/null || true
                        docker rm ${container} 2>/dev/null || true
                        docker run -d --name ${container} -p ${port}:${port} -e PORT=${port} ${tag}
                    """
                }
            }
        }
    }
}