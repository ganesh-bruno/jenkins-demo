pipeline {
    agent any
    environment {
        // Capture the path to Node.js 18. This variable will be available globally.
        // This requires the Node.js tool to be configured in Jenkins.
        NODE_HOME = tool 'Node.js 18'
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Setup Node.js & Install Bruno CLI') {
            steps {
                // Ensure the bin directory is added to PATH for all subsequent sh commands
                withEnv(["PATH+NODE=${NODE_HOME}/bin"]) {
                    echo 'Verifying Node.js and npm versions...'
                    sh 'node -v'
                    sh 'npm -v'
                    echo 'Installing Bruno CLI globally...'
                    sh 'npm install -g @usebruno/cli'
                    sh 'bru --version'
                }
            }
        }
        stage('Run API Tests') {
            steps {
                withEnv(["PATH+NODE=${NODE_HOME}/bin"]) { // Still need this or declare it inside script block
                    echo 'Executing Bruno API tests...'
                    sh 'bru run --env ci --reporter-html results.html'
                }
            }
        }
        stage('Archive Test Results') {
            steps {
                echo 'Archiving test report...'
                archiveArtifacts artifacts: 'results.html', fingerprint: true
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'API Tests Passed Successfully!'
        }
        failure {
            echo 'API Tests Failed! Check console output for details.'
        }
    }
}