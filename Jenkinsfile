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
                    npm install
                    npm install pm2 -g
                '''
            }
        }

        stage('Build Next.js') {
            steps {
                sh '''
                    echo "Building Next.js App..."
                    rm -rf .next
                    npm run build
                '''
            }
        }

        stage('Deploy with PM2') {
            steps {
                sh '''
                    echo "Deploying using PM2..."
                    mkdir -p ${APP_DIR}
                    cp -r . ${APP_DIR}/
                    cd ${APP_DIR}

                    pm2 stop ${APP_NAME} || true
                    pm2 delete ${APP_NAME} || true
                    pm2 start npm --name "${APP_NAME}" -- start
                    pm2 save

                    # Setup PM2 startup (only needed once, so it's fine to skip on re-runs)
                    sudo pm2 startup systemd -u ec2-user --hp /home/ec2-user
                '''
            }
        }
    }
}
