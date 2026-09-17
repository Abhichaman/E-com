pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend (Maven)') {
            steps {
                dir('Ecommerce-Backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend (Vite)') {
            steps {
                dir('Ecommerce-Frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Deploy via Docker Compose') {
            steps {
                sh '''
                    docker compose down
                    docker compose build --no-cache
                    docker compose up -d
                '''
            }
        }

        stage('Verify Database Health & Schema') {
            steps {
                sh '''
                    sleep 5
                    docker compose exec -T mysql mysql -u root -proot ecomdb -e "ALTER TABLE product MODIFY COLUMN image_date LONGBLOB;" || true
                '''
            }
        }
    }

    post {
        success {
            echo "Project deployed successfully on port 80!"
        }
        failure {
            echo "Deployment failed. Check Jenkins console output."
        }
        always {
            cleanWs notFailBuild: true, deleteDirs: true
        }
    }
}
