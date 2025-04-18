pipeline {
    agent any
    
    tools {
        nodejs "NodeJS" // Name configured in Global Tools
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rakeshkanneeswaran/jenkins-test-app'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Deploy') {
            steps {
                sh '''
                    # Stop existing app if running
                    pm2 stop my-nextjs-app || true
                    pm2 delete my-nextjs-app || true
                    
                    # Start new instance
                    pm2 start npm --name "my-nextjs-app" -- start
                    pm2 save
                    
                    # Set up startup script if not already done
                    pm2 startup | grep -v "\[PM2\]" | bash
                '''
            }
        }
    }
}