// Declarative Jenkins Pipeline
pipeline {

    // Run this pipeline on any available Jenkins agent (node)
    agent any

    // Automatically install and configure required tools
    tools {
        // Uses the NodeJS tool configured in:
        // Manage Jenkins → Global Tool Configuration
        nodejs 'NodeJS'
    }

    // Global environment variables accessible in all stages
    environment {
        // GitHub repository URL
        REPO_URL = 'https://github.com/uttamb15/ts-to-js-demo.git'

        // Target branch to checkout and push changes
        BRANCH_NAME = 'main'
    }

    // Define pipeline stages
    stages {

        // Stage 1: Clone source code from GitHub
        stage('Checkout Code') {
            steps {
                // Clone specific branch using Jenkins stored credentials
                git branch: "${BRANCH_NAME}",
                    credentialsId: 'github_credentials',  // Jenkins credential ID
                    url: "${REPO_URL}"                     // Repository URL
            }
        }

        // Stage 2: Install npm dependencies
        stage('Install Dependencies') {
            steps {
                // 'bat' is used because this is running on a Windows agent
                // For Linux agents, use 'sh'
                bat 'npm install'
            }
        }

        // Stage 3: Compile TypeScript files into JavaScript
        stage('Compile TypeScript') {
            steps {
                // Runs the TypeScript compiler
                // It generates JS files (usually inside dist/ as per tsconfig.json)
                bat 'npx tsc'
            }
        }

        // Stage 4: Commit generated JS files and push to GitHub
        stage('Commit & Push JS') {
            steps {

                // Securely retrieve GitHub credentials from Jenkins
                withCredentials([usernamePassword(
                    credentialsId: 'github_credentials',
                    usernameVariable: 'GIT_USER',   // Injected username variable
                    passwordVariable: 'GIT_PASS'    // Injected password/token variable
                )]) {

                    // Execute multiple Git commands inside Windows shell
                    bat """
                        // Set Git identity for automated commits
                        git config user.email "uttamsbaba10@gmail.com"
                        git config user.name "uttamb15"

                        // Add compiled JavaScript files to staging area
                        git add dist/

                        // Commit changes
                                               // || echo No changes prevents failure if nothing changed
                        git commit -m "Auto-generated JS from TypeScript" || echo No changes

                        // Push changes back to the same branch using credentials
                        // Credentials are passed securely via environment variables
                        git push https://%GIT_USER%:%GIT_PASS%@github.com/uttamb15/ts-to-js-demo.git ${BRANCH_NAME}
                    """
                }
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
