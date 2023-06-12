pipeline {
    agent any
    options {
        timeout(time: 5, unit: 'MINUTES')
    }
    environment{
        ACCESS_KEY = credentials('secret-key')
        SLACK_CHANNEL = " Lucas Braga"
    }
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                python3 -m venv .venv
                . .venv/bin/activate
                pip3 install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh '''
                . .venv/bin/activate
                pytest tests/units
                '''
            }
        }
        stage('Deploy'){
            parallel{
                stage('Production') {
                    when{
                        branch 'main'
                    }
                    steps {
                        echo 'Deliver....'
                        sh '''
                        echo "doing delivery stuff.. at production"
                        '''
                    }
                    post{
                        success{
                            slackSend(channel: "${env.SLACK_CHANNEL}", message: "New release in the production", color: "good")
                        }
                        failure{
                             slackSend(channel: "${env.SLACK_CHANNEL}", message: "Failed to deploy in the production", color: "danger")
                        }
                    }
                }
                stage('Homolog') {
                    when {
                        tag "rc-*" 
                    }
                    steps {
                        echo 'Deliver....'
                        sh '''
                        echo "doing delivery stuff.. at homolog"
                        '''
                    }
                    post{
                        success{
                            slackSend(channel: "${env.SLACK_CHANNEL}", message: "New release available in the homolog", color: "good")
                        }
                        failure{
                             slackSend(channel: "${env.SLACK_CHANNEL}", message: "Failed to deploy in the homolog", color: "danger")
                        }
                    }
                }
                stage('Develop') {
                    when{
                        branch 'develop'
                    }
                    steps {
                        echo 'Deliver....'
                        sh '''
                        echo "doing delivery stuff.. at develop"
                        '''
                    }
                }
            }
        }
    }
    post{
        failure{
            slackSend(channel: "${env.SLACK_CHANNEL}",
            message: "Build Started: ${env.JOB_NAME} View pipeline, ${env.RUN_DISPLAY_URL} failed",
            color: "danger")
        }
        success{
            slackSend(channel: "${env.SLACK_CHANNEL}",
            message: "Build Started: ${env.JOB_NAME}, View pipeline, ${env.RUN_DISPLAY_URL} success",
            color: "good")
            script{
                if (env.BRANCH_NAME.contains('PR')) {
                    slackSend(channel: "${env.SLACK_CHANNEL}",
                    message: "Pull Request to review ${env.GIT_URL}, Jenkins build ${env.JOB_NAME}",
                    color: "good")
                }
            }
        }  
    }
}