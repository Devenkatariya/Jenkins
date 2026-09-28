// Import the centralized library we configured in the main system settings
@Library('company-shared-lib') _

pipeline {
    agent any
    
    stages {
        stage('1. Initialize & Ingestion') {
            steps {
                cleanWs()
                checkout scm
            }
        }
        
        stage('2. Quality Verification') {
            steps {
                // Invoking Tool B (runTests.groovy) dynamically from the shared repo warehouse
                runTests junit: 'test-results/unit-tests.xml'
            }
        }
        
        stage('3. Compile Container Image') {
            steps {
                // Invoking Tool A (dockerBuild.groovy) natively from the library assets
                dockerBuild image: 'fraud-inference-service', tag: "${env.BUILD_NUMBER}"
            }
        }
    }
    
    post {
        success {
            // FIXED: Wrapped object-method call inside a script block
            script {
                notify.success("Pipeline Process Execution Build #${env.BUILD_NUMBER} completed cleanly!")
            }
        }
    }
}
