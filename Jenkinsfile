pipeline {
    agent any
    tools {
        nodejs "NodeJS_18"
    }
environment {
    APP_NAME = "my-nextjs-app"
    APP_DIR = "/home/ubuntu/app"
    APP_USER = "ubuntu"
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
            # Clean and copy files
            sudo -u ubuntu bash -c '
                rm -rf ${APP_DIR}/*
                cp -r ${WORKSPACE}/* ${APP_DIR}/
                chmod -R 755 ${APP_DIR}
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