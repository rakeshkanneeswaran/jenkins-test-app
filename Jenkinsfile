pipeline {
    agent any
    tools {
        nodejs "NodeJS_18"
    }
    environment {
        APP_NAME = "my-nextjs-app"
        APP_DIR = "/home/ubuntu/app"
        APP_USER = "ubuntu"
        PATH = "/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:${env.PATH}"  // Important for Jenkins
    }
    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/rakeshkanneeswaran/jenkins-test-app'
            }
        }

        stage('Install Dependencies & Build') {
            steps {
                sh '''
                    npm install --production=false
                    npm install -g pm2
                    npm run build
                '''
            }
        }

        stage('Deploy App to EC2') {
            steps {
                sh """
                    sudo -u ${APP_USER} bash -c '
                        # Clean app directory
                        rm -rf ${APP_DIR}/*
                        
                        # Copy Jenkins workspace files to app dir
                        cp -r ${WORKSPACE}/* ${APP_DIR}/
                        
                        # Go to app dir
                        cd ${APP_DIR}

                        npm install -g pm2
                        
                        
                        # Install dependencies for production
                        npm install --production
                        
                        # Restart app with PM2
                        pm2 delete ${APP_NAME} || true
                        pm2 start npm --name "${APP_NAME}" -- run start
                        pm2 save
                        pm2 list
                    '
                """
            }
        }
    }
}
