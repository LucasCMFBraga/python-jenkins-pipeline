pipeline {
    agent any
    parameters{
        string(name: 'DEV-CHANNEL', defaultValue: 'Lucas Braga', description: 'Channel in the case of pipeline failures')
        string(name: 'QA-CHANNEL', defaultValue: 'Lucas Braga', description: 'Channel to notify new release to QA')
        string(name: 'PR-CHANNEL', defaultValue: 'Lucas Braga', description: 'Channel for PR review')
    }
    options {
        timeout(time: 5, unit: 'MINUTES')
    }
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                python3 -m venv .venv
                source .venv/bin/activate
                pip3 install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh '''
                source .venv/bin/activate
                pytest tests/units
                '''
            }
        }
        stage('Deliver Production') {
            when{
                branch 'main'
            }
            steps {
                echo 'Deliver....'
                sh '''
                echo "doing delivery stuff.. at production"
                '''
            }
        }
        stage('Deliver Homolog') {
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
                    slackSend channel: '${params.QA-CHANNEL}', message: "New release available"
                }
            }
        }
        stage('Deliver Develop') {
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
    post{
        failure{
            slackSend channel: '${params.DEV-CHANNEL}', message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER} failed"
        }
        success{
            slackSend channel: '${params.PR-CHANNEL}', message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER} success"
            // slackSend channel: '${params.PR-CHANNEL}', message: "Pull Request to review ${env.GIT_URL}, Jenkins build ${env.BUILD_URL}"
        }        
    }
}