pipeline {
    agent any

    // Use NodeJS installed by Jenkins plugin
    tools {
        nodejs 'NodeJS'
    }

    environment {
        FIREBASE_TOKEN = credentials('FIREBASE_TOKEN')
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'node -v'
                sh 'npm -v'
                sh 'npm install'
                sh 'npm install -g firebase-tools'
            }
        }

        stage('Build Angular App') {
            steps {
                echo 'Building Angular app...'
                sh 'npx ng build --configuration production'
            }
        }

        stage('Deploy to Firebase') {
            steps {
                echo 'Deploying to Firebase Hosting...'
                sh 'firebase deploy --token $FIREBASE_TOKEN'
            }
        }
    }

    post {
        success {
            echo '✅ Angular app deployed successfully to Firebase!'
        }
        failure {
            echo '❌ Build or deployment failed. Check logs.'
        }
    }
}