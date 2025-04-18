pipeline {
    agent any
    tools {
        nodejs "NodeJS_18"
    }
    environment {
        APP_NAME = "my-nextjs-app"
        APP_DIR = "/home/ec2-user/app"
        APP_PORT = "3000"
        NODE_ENV = "production"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/rakeshkanneeswaran/jenkins-test-app'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    npm install --production=false
                    npm install -g pm2
                    npm run build
                '''
            }
        }
        stage('Deploy with PM2') {
            steps {
                sh '''
                    # Clean and prepare target directory
                    sudo rm -rf ${APP_DIR}/*
                    sudo cp -r * ${APP_DIR}/
                    
                    # Fix permissions (crucial!)
                    sudo chown -R ec2-user:ec2-user ${APP_DIR}
                    sudo chmod -R 755 ${APP_DIR}
                    
                    # Run PM2 as ec2-user
                    sudo -u ec2-user bash -c '
                        cd ${APP_DIR}
                        pm2 stop ${APP_NAME} || true
                        pm2 delete ${APP_NAME} || true
                        pm2 start npm --name "${APP_NAME}" -- start
                        pm2 save
                    '
                '''
            }
        }
    }
}