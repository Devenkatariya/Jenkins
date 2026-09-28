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
                // Invoking runTests.groovy dynamically from the shared repo warehouse
                runTests junit: 'test-results/unit-tests.xml'
            }
        }
        
        stage('3. Compile Container Image') {
            steps {
                // Invoking dockerBuild.groovy natively from the library assets
                dockerBuild image: 'fraud-inference-service', tag: "${env.BUILD_NUMBER}"
            }
        }
    }
    
    post {
        success {
            // Invoking notify.groovy for automated infrastructure tracking signals
            notify.success("Pipeline Process Execution Build #${env.BUILD_NUMBER} completed cleanly!")
        }
    }
}
