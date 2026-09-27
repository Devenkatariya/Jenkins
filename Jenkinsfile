pipeline {
    agent any
    
    options {
        timeout(time: 15, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
    }
    
    environment {
        APP_NAME = 'fraud-inference-service'
    }
    
    parameters {
        choice(name: 'TARGET_ENV', choices: ['staging', 'production'], description: 'Which environment are you deploying to?')
        booleanParam(name: 'SKIP_TESTS', defaultValue: false, description: 'Bypass testing phase if checked')
    }
    
    stages {
        stage('1. Initialize') {
            steps {
                cleanWs()
                echo "Workspace prepared for ${env.APP_NAME}."
            }
        }
        
        stage('2. Test') {
            when { not { expression { return params.SKIP_TESTS } } }
            steps {
                echo "Running unit tests..."
            }
        }
        
        // ── STAGE 5 from PDF: Assemble Application Assets ──
        stage('3. Build Packaged Assets') {
            steps {
                echo "Compiling code assets and creating deployment packages..."
            }
        }
        
        // ── STAGE 9 from PDF: Manual Human Gatekeeper ──
        stage('4. Awaiting Deployment Approval') {
            when {
                // This gate triggers ONLY if the user picked production from the dropdown form
                expression { params.TARGET_ENV == 'production' }
            }
            steps {
                echo "🚨 Production deployment requested! Pausing pipeline for verification..."
                
                // Halts the pipeline automatically for up to 24 hours until a lead clicks approve
                timeout(time: 24, unit: 'HOURS') {
                    input message: "Authorize release of ${env.APP_NAME} directly to PRODUCTION?", ok: "Release Deploy"
                }
            }
        }
        
        // ── STAGE 10 from PDF: Production Live Release ──
        stage('5. Deploy Live') {
            steps {
                echo "🚀 Execution initiated: Shipping software updates out to environment: [${params.TARGET_ENV.toUpperCase()}]"
                echo "Deployment successfully executed! ✅"
            }
        }
    }
}
