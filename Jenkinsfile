pipeline {
    agent any
    tools {
        nodejs "NodeJS_18"
    }
    environment {
        APP_NAME = "my-nextjs-app"
        APP_DIR = "/home/ubuntu/app"
        APP_USER = "ubuntu"
        PATH = "/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:${env.PATH}"  // Critical fix
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
      stage('Prepare Directory') {
    steps {
        sh "echo 'Directory already prepared'"
    }
}
        stage('Deploy with PM2') {
    steps {
        sh """
            sudo -u ${APP_USER} bash -c '
                # Clean and copy files
                rm -rf ${APP_DIR}/*
                cp -r ${WORKSPACE}/* ${APP_DIR}/
                
                # Install dependencies and start
                cd ${APP_DIR}
                npm install --production
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