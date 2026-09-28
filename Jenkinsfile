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
                runTests junit: 'test-results/unit-tests.xml'
            }
        }
        
        stage('3. Compile Container Image') {
            steps {
                dockerBuild image: 'fraud-inference-service', tag: "${env.BUILD_NUMBER}"
            }
        }
        
        // ── NEW STAGE FROM PAGES 13-14 OF THE PDF: ARTIFACT MANAGEMENT ──
        stage('4. Package & Archive Release Records') {
            steps {
                echo "Packaging metadata logs and compiling release verification records..."
                
                // Generating an artifact release directory bundle inside the worker engine workspace
                sh '''
                mkdir -p operational-artifacts
                echo "Release-Package-Name: fraud-inference-service" > operational-artifacts/release-manifest.txt
                echo "Build-Run-ID: #${BUILD_NUMBER}" >> operational-artifacts/release-manifest.txt
                echo "Deployment-Timestamp: $(date)" >> operational-artifacts/release-manifest.txt
                echo "Git-Revision: ${GIT_COMMIT}" >> operational-artifacts/release-manifest.txt
                
                # Generating validation checksum hashes for security integrity tracking
                sha256sum operational-artifacts/* > operational-artifacts/checksums.sha256
                '''
                
                // Saving the folder permanently inside the Jenkins system vault storage unit
                archiveArtifacts artifacts: 'operational-artifacts/**/*', fingerprint: true, onlyIfSuccessful: true
                
                echo "Build records securely moved into persistent storage vault! ✅"
            }
        }
    }
    
    post {
        success {
            script {
                notify.success("Pipeline Process Execution Build #${env.BUILD_NUMBER} completed cleanly!")
            }
        }
    }
}
