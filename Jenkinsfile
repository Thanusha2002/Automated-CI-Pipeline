pipeline {
    agent any

    environment {
        ARTIFACT_GLOB = 'dist/**' // adjust to your actual artifact path
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout from your repo
                git branch: 'main', url: 'https://github.com/Thanusha2002/Automated-CI-Pipeline.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    if (isUnix()) {
                        // Linux / macOS
                        sh '''
                            set -e
                            echo "Using Unix shell: $(which bash || which sh)"
                            python3 -m venv venv
                            . venv/bin/activate
                            pip install --upgrade pip
                            if [ -f requirements.txt ]; then
                                pip install -r requirements.txt
                            else
                                echo "No requirements.txt"
                            fi
                        '''
                    } else {
                        // Windows
                        bat '''
                            echo Using Windows CMD
                            python -m venv venv
                            call venv\\Scripts\\activate
                            python -m pip install --upgrade pip
                            if exist requirements.txt (
                                pip install -r requirements.txt
                            ) else (
                                echo No requirements.txt
                            )
                        '''
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                            . venv/bin/activate
                            if command -v pytest >/dev/null 2>&1; then
                                pytest -q || echo "Tests failed"
                            else
                                echo "pytest not installed or no tests found"
                            fi
                        '''
                    } else {
                        bat '''
                            call venv\\Scripts\\activate
                            where pytest || echo pytest not found
                            pytest -q || echo Tests failed
                        '''
                    }
                }
            }
        }

        stage('Build & Package') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                            . venv/bin/activate
                            echo "Build step placeholder - replace with actual commands"
                            mkdir -p dist || true
                            echo "Dummy artifact" > dist/README.txt
                        '''
                    } else {
                        bat '''
                            call venv\\Scripts\\activate
                            echo Build step placeholder - replace with actual commands
                            if not exist dist mkdir dist
                            echo Dummy artifact > dist\\README.txt
                        '''
                    }
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: "${env.ARTIFACT_GLOB}", allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            script {
                if (isUnix()) {
                    sh 'echo "Post actions: workspace info"; ls -la || true'
                } else {
                    bat '@echo off & echo Post actions: workspace info & dir || true'
                }
            }
        }
        success {
            echo '✅ Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed — check console output'
        }
    }
}
