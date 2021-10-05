import java.util.regex.Pattern;

def appname = "Runner" // This refers to the flutter 'Runner' target.
def xcarchive = "${appname}.xcarchive"

pipeline {
    agent { label 'master' }
    environment {
        DEVELOPER_DIR="/Applications/Xcode.app/Contents/Developer/"
        
        // GIT_BRANCH= HEAD/BRANCH
        BRANCH_NAME = "${GIT_BRANCH.split('/').size() > 1 ? GIT_BRANCH.split('/')[1..-1].join('/') : GIT_BRANCH}"
        DEPLOYMENT_SYSTEM="${BRANCH_NAME == 'develop' ? 'testflight' : 'firebase'}"

        //fastlane
        FASTLANE_PASSWORD="<your password>"
        LANG="en_US.UTF-8"
        LC_ALL="en_US.UTF-8"
        FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD="<your password>"
        FASTLANE_SESSION='<YOUR SESSION>'
        MATCH_PASSWORD='<your password>'
        GIT_AUTHORIZATION = "<username>:<token>"
        FIREBASE_TOKEN= "<your token>"
        SLACK_URL= "<your slack url>"
    }
  
    stages {
        stage ('Checkout') {
            steps {
                checkout scm  
            }
        }

        stage ('Check git commit for build') {
            steps {
                script {
                    env.GIT_COMMIT_MSG = sh (script: 'git log -1 --pretty=%B ${GIT_COMMIT}', returnStdout: true).trim()
                    
                    if(!Pattern.matches("(release.*|build.*)", "${GIT_COMMIT_MSG.toLowerCase()}")){
                        echo "Commit ${GIT_COMMIT_MSG.toLowerCase()} can't build app"
                        echo 'Please commit with a message containing the string "release" or "build" to be build app' 

                        sh 'false'
                    }
                }
            }
        }

        stage ('Flutter install depedencies') {
            steps {
                sh "flutter clean"
                sh "flutter pub get"
                sh "cd ios && pod install && cd .."
            }
        }

        stage ('Sign in and build') {
            steps {
                dir("ios"){
                    sh "fastlane sign_in"
                    sh "fastlane build"
                }
                dir('android'){
                    // build for android
                    sh "fastlane build"    
                }
            }
        }

        stage ("Deploy app") {
            steps {
                dir("ios"){
                    echo "Deploy app to ${env.DEPLOYMENT_SYSTEM}"
                    sh "fastlane deloy_app 'system':'${env.DEPLOYMENT_SYSTEM}'"
                }
                dir("android"){
                    // sh "fastlane distribute"
                    // code build for android
                }
            }
        }

        stage ("Push notification and update repo") {
            steps {
                dir("ios"){
                    echo "Push notification to slack ${env.DEPLOYMENT_SYSTEM}"
                    sh "fastlane push_to_slack 'system':'${env.DEPLOYMENT_SYSTEM}'"

                    echo "Update repository"
                    sh "fastlane update_repo 'branch':'${env.BRANCH_NAME}'"
                }
            }
        }
    
        stage ('Cleanup') {
            steps {
                sh "flutter clean"
            }
        }
    }
    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir() /* clean up our workspace */
        }
        success {
            echo 'Success'
        }       
    }
}
 