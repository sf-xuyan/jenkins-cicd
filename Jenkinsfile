pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'setup env and install plugins ...'
            }
        }
        
        stage('Test') {
            steps {
                echo 'run apex test ...'
                echo 'all tests passed -> code coverage: 88%'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'start to deploy ...'
                echo 'deploy successfully ...'
            }
        }
    }
}