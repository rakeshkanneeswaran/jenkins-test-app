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
                    # Copy files
                    sudo -u ${APP_USER} rsync -a --delete ${WORKSPACE}/ ${APP_DIR}/
                    
                    # PM2 commands
                    sudo -u ${APP_USER} bash -c '
                        export PATH="/usr/local/bin:$PATH"
                        cd ${APP_DIR}
                        pm2 list | grep ${APP_NAME} && pm2 stop ${APP_NAME} || echo "App not running"
                        pm2 list | grep ${APP_NAME} && pm2 delete ${APP_NAME} || echo "App not registered"
                        pm2 start npm --name "${APP_NAME}" -- run start
                        pm2 save
                        pm2 list
                    '
                """
            }
        }
    }
   
}