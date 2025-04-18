pipeline {
    agent any
    
    tools {
        nodejs "NodeJS"  // Matches the name in Global Tools
    }
    
    environment {
        APP_NAME = "my-nextjs-app"
        APP_PORT = 3000
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/rakeshkanneeswaran/jenkins-test-app'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Build Next.js') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Deploy with PM2') {
            steps {
                sh '''
                    # Stop existing app if running
                    pm2 stop ${APP_NAME} || true
                    pm2 delete ${APP_NAME} || true
                    
                    # Start new instance
                    pm2 start npm --name "${APP_NAME}" -- start
                    pm2 save
                    
                    # Ensure PM2 starts on reboot
                    pm2 startup | grep -v "[PM2]" | bash
                '''
            }
        }
    }
    
    post {
        success {
            echo "Deployment successful! Access at: http://${env.APP_PORT}"
        }
        failure {
            echo "Deployment failed. Check logs."
        }
    }
}