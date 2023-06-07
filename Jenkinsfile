pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                pip3 install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh '''
                pytest tests/units
                '''
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deliver....'
                sh '''
                echo "doing delivery stuff.."
                '''
            }
        }
    }
}