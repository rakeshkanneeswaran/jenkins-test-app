pipeline {
    agent any

    tools {
        nodejs "NodeJS_18" // Make sure Jenkins has this tool name configured
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
                git branch: 'main',
                    url: 'https://github.com/rakeshkanneeswaran/jenkins-test-app'
            }
        }

       stage('Install Dependencies') {
    steps {
        sh '''
            echo "Installing dependencies..."
            npm install --production=false  # Ensures devDependencies are installed
            npm install -g pm2
            echo "Building Next.js App..."
            npm run build
            echo "Deploying using PM2..."
            mkdir -p ${APP_DIR}
            cp -r . ${APP_DIR}/
            cd ${APP_DIR}
            pm2 stop ${APP_NAME} || true
            pm2 delete ${APP_NAME} || true
            pm2 start npm --name "${APP_NAME}" -- start
            pm2 save
            sudo pm2 startup systemd -u ec2-user --hp /home/ec2-user
        '''
    }
}

     

    }
}
