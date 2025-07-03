pipeline {
    agent any // Run the pipeline on any available Jenkins agent
    stages {
        stage('Checkout Code') {
            steps {
                // Clones the repository to the Jenkins workspace
                checkout scm
            }
        }
        stage('Setup Node.js & Install Bruno CLI') {
            steps {
                // Makes the Node.js installation (configured in Jenkins Tools) available
                tool 'Node.js 18' // Use the exact name configured in Jenkins Global Tool Configuration
                echo 'Verifying Node.js and npm versions...'
                sh 'node -v' // Verify Node.js is on PATH
                sh 'npm -v'  // Verify npm is on PATH
                echo 'Installing Bruno CLI globally...'
                sh 'npm install -g @usebruno/cli' // Install Bruno CLI
                sh 'bru --version' // Verify Bruno CLI installation
            }
        }
        stage('Run API Tests') {
            steps {
                // Ensure Node.js is available if this stage runs in a separate context
                tool 'Node.js 18'
                echo 'Executing Bruno API tests...'
                // Run tests with 'ci' environment and generate an HTML report
                sh 'bru run --env ci --reporter-html results.html'
            }
        }
        stage('Archive Test Results') {
            steps {
                echo 'Archiving test report...'
                // Save the generated HTML report as a build artifact
                archiveArtifacts artifacts: 'results.html', fingerprint: true
            }
        }
    }
    post {
        // Actions to run after the main pipeline stages complete
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
