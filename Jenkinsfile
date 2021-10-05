pipeline {

    agent none

    environment {
        ///
        //login firebase: sudo firebase login:ci --no-localhost --debug
        FIREBASE_TOKEN = "<Token>"
    }
    
    stages {
        stage('Install dependencies and build'){
            agent {
                docker { image 'node:12-alpine' }
            }
            steps {
                echo 'install dependencies'
                sh 'npm install'

                echo 'build bundel'
                sh 'npm run build'
            }  
        }
        stage('Deploy to server') {
            agent {
                docker { image 'andreysenov/firebase-tools:9.18.0-node12-alpine' }
            }
            steps {
                sh 'firebase deploy --only hosting'
            }
        }
    }
    post {
       
        success {
            echo 'Success'
        }   
    }
}

 