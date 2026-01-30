// Auto-deploy to Firebase: runs on every push (poll or webhook). Uses Node on Jenkins agent.
pipeline {
    agent any

    triggers {
        // Auto-deploy: poll repo every 2 min; or use GitHub webhook for instant deploy on push
        pollSCM('H/2 * * * *')
    }

    environment {
        FIREBASE_TOKEN = credentials('firebase-token')
        PATH = "/usr/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'node -v && npm -v'
                sh 'npm ci'
                sh 'npm install -g firebase-tools'
            }
        }

        stage('Build Angular') {
            steps {
                sh 'npx ng build --configuration production'
            }
        }

        stage('Deploy to Firebase') {
            steps {
                sh 'firebase deploy --token $FIREBASE_TOKEN --non-interactive'
            }
        }
    }

    post {
        success {
            echo '✅ Auto-deployed to Firebase Hosting.'
        }
        failure {
            echo '❌ Build or deploy failed. Check logs.'
        }
    }
}
