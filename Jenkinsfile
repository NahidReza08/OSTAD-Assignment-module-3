pipeline {
    agent { label 'WSL' }

    environment {
        NODE_ENV = 'development'
    }

    stages {

        stage('Clone') {
            steps {
                echo '📥 Cloning latest code from GitHub...'
                git url: 'https://github.com/NahidReza08/OSTAD-Assignment-module-3', branch: 'main'
            }
        }

        stage('Install') {
            steps {
                echo '📦 Installing dependencies...'
                sh 'npm install'
                
                echo '📦 Installing mocha-junit-reporter...'
                sh 'npm install mocha-junit-reporter'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                sh 'npm run check'
                
                echo '🧪 Publishing test results...'
                sh 'npx mocha --reporter mocha-junit-reporter --reporter-options mochaFile=test_results.xml test/**/*.test.js'
                junit '**/test_results.xml'
            }
        }
        
         stage('Deploy') {
            steps {
                echo 'Stopping existing app if running...'
                sh 'pm2 delete node-app || true'

                echo 'Starting application using PM2...'
                sh 'pm2 start "./src/server.js" --name node-app'

                echo 'Saving PM2 process list...'
                sh 'pm2 save'
            }
        }

    }

    post {
        always {
            echo '📝 Pipeline execution complete.'
        }
        success {
            echo '✅ All stages completed successfully!'
             slackSend (
                color: '#36a64f',
                message: "✅ Jenkins Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
            );
        }
        failure {
            echo '❌ One or more stages failed.'
            slackSend (
                color: '#FF0000',
                message: "❌ Jenkins Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
            );
        }
    }
}