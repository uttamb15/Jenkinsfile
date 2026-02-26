pipeline {
    agent any

    // Use NodeJS configured in Jenkins (Manage Jenkins → Global Tool Configuration)
    tools {
        nodejs 'NodeJS'
    }

    environment {
        REPO_URL    = 'https://github.com/uttamb15/ts-to-js-demo.git'
        BRANCH_NAME = 'main'
        LOCAL_OUTPUT_DIR = 'C:\\ts-js-output'
    }

    stages {

        stage('Checkout Code') {
            steps {
                // Pull latest code from GitHub
                git branch: "${BRANCH_NAME}",
                    credentialsId: 'github_credentials',
                    url: "${REPO_URL}"
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install npm dependencies from package.json
                bat 'npm install'
            }
        }

        stage('Compile TypeScript') {
            steps {
                // Compile TypeScript → JavaScript (uses tsconfig.json)
                bat 'npx tsc'
            }
        }

        stage('Store JS Locally') {
            steps {
                bat """
                    REM Create local output directory if it does not exist
                    if not exist "%LOCAL_OUTPUT_DIR%" (
                        mkdir "%LOCAL_OUTPUT_DIR%"
                    )

                    REM Copy compiled JS files to local system
                    copy /Y dist\\*.js "%LOCAL_OUTPUT_DIR%\\"
                """
            }
        }

        stage('Archive JS Artifacts') {
            steps {
                // Store JS files inside Jenkins for this build
                archiveArtifacts artifacts: 'dist/**/*.js', fingerprint: true
            }
        }
    }

    // Post-build actions
    post {

        // Executes if all stages complete successfully
        success {
            echo "Build Successful"
        }

        // Executes if any stage fails
        failure {
            echo "Build Failed"
        }
    }
}
