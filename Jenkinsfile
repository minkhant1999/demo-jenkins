pipeline {
    agent any

    environment {
        FIREBASE_TOKEN = credentials('firebase_token')
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Angular') {
            steps {
                script {
                    docker.image('node:18-alpine').inside {
                        sh '''
                          npm install
                          npm run build
                        '''
                    }
                }
            }
        }

        stage('Deploy to Firebase') {
            steps {
                script {
                    docker.image('node:18-alpine').inside {
                        sh '''
                          npm install -g firebase-tools
                          firebase deploy --only hosting --token "$FIREBASE_TOKEN"
                        '''
                    }
                }
            }
        }
    }
}
