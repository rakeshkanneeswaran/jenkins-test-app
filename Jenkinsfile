pipeline {
    agent any

    tools {
        nodejs "NodeJS_18"
    }

    environment {
        APP_NAME = "my-nextjs-app"
        APP_DIR = "/home/ec2-user/app"
        APP_PORT = 3000
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
                    # Install dependencies including devDependencies
                    npm install

                    # Install TailwindCSS globally
                    npm install -g tailwindcss

                    # If tailwind.config.js does not exist, create it
                    if [ ! -f tailwind.config.js ]; then
                        echo "Initializing Tailwind CSS config..."
                        tailwindcss init --yes
                    fi
                '''
            }
        }

        stage('Build Next.js') {
            steps {
                sh '''
                    rm -rf .next
                    NODE_ENV=production npm run build
                '''
            }
        }

        stage('Deploy with PM2') {
            steps {
                script {
                    sh """
                        mkdir -p ${APP_DIR}
                        cp -r * ${APP_DIR}/
                        chown -R ec2-user:ec2-user ${APP_DIR}
                        cd ${APP_DIR}
                        pm2 stop ${APP_NAME} || true
                        pm2 delete ${APP_NAME} || true
                        pm2 start npm --name "${APP_NAME}" -- start
                        pm2 save
                        sudo env PATH=\$PATH:/usr/bin /usr/local/bin/pm2 startup -u ec2-user --hp /home/ec2-user
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "Deployment successful! Access at: http://${env.APP_PORT}"
            slackSend(color: "good", message: "SUCCESS: ${env.APP_NAME} deployed")
        }
        failure {
            echo "Deployment failed. Check logs."
            slackSend(color: "danger", message: "FAILED: ${env.APP_NAME} deployment")
        }
    }
}
