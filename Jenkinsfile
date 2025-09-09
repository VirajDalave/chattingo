@Library("Shared") _
pipeline {
    agent {
        label 'chattingo'
    }
    
    environment {
        MYSQL_DATABASE          = 'chattingo'
        CORS_ALLOWED_ORIGINS    = 'http://chattingo.virajdalave.shop,http://127.0.0.1:3000'
        CORS_ALLOWED_METHODS    = 'GET,POST,PUT,DELETE'
        REACT_APP_API_URL       = 'http://72.60.111.84:8080'
    }

    stages {
        stage('Git Clone') {
            steps {
                echo "This is cloning the code"
                sh "git clone https://github.com/VirajDalave/chattingo.git"
                // git url: "https://github.com/VirajDalave/chattingo.git", branch: "main"
                echo "Code cloned successsfully"
            }
        }
        
        stage('Image Build') {
            steps {
                script {
                    docker_build('virajdalave','chattingo-app','${BUILD_NUMBER}','./chattingo/backend')
                    docker_build('virajdalave','chattingo-web','${BUILD_NUMBER}', './chattingo/frontend')
                }
            }
        }
        
        stage('Filesystem Scan') {
            steps {
                echo "FS"
            }
        }
        
        stage('Image Scan') {
            steps {
                echo "IS"
            }
        }
        
        stage('Push to Registry') {
            steps {
                script{
                    docker_push("virajdalave", "chattingo-app", '${BUILD_NUMBER}')
                    docker_push("virajdalave", "chattingo-web", '${BUILD_NUMBER}')
                }
            }
        }
        
        stage('Update Compose') {
            steps {
                sh "sed -i 's|chattingo-app:latest|chattingo-app:${BUILD_NUMBER}|g' ./chattingo/docker-compose.prod.yml"
                sh "sed -i 's|chattingo-web:latest|chattingo-web:${BUILD_NUMBER}|g' ./chattingo/docker-compose.prod.yml"
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    deploy_chattingo()
                }
                
                // withCredentials([
                //     string(credentialsId: 'DB_PASS', variable: 'DB_PASS'),
                //     string(credentialsId: 'JWT_SECRET', variable: 'JWT_SECRET'),
                //     string(credentialsId: 'SPRING_DATASOURCE_URL', variable: 'SPRING_DATASOURCE_URL'),
                //     string(credentialsId: 'SPRING_DATASOURCE_USERNAME', variable: 'SPRING_DATASOURCE_USERNAME'),
                //     usernamePassword('credentialsId':"docker-creds",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")
                    
                // ]) {
                //     sh '''
                //       docker login -u ${dockerHubUser} -p ${dockerHubPass}
                //       docker compose -f ./chattingo/docker-compose.prod.yml down || true
                //       docker compose -f ./chattingo/docker-compose.prod.yml up -d
                //     '''
                // }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
