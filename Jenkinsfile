pipeline {
  agent any

  options {
    // keep a few builds
    buildDiscarder(logRotator(numToKeepStr: '20'))
    timestamps()
  }

  environment {
    // adjust if your repo uses a different artifact directory
    ARTIFACT_GLOB = 'dist/**' // change to '**/target/*.jar' or similar as needed
  }

  stages {

    stage('Checkout SCM') {
      steps {
        // Declarative checkout
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        script {
          if (isUnix()) {
            // Linux / macOS flow
            sh '''
              set -e
              echo "Using Unix shell: $(which bash || which sh)"
              python3 -m venv venv
              . venv/bin/activate
              pip install --upgrade pip
              if [ -f requirements.txt ]; then pip install -r requirements.txt; else echo "No requirements.txt"; fi
            '''
          } else {
            // Windows flow
            // Use 'call' to ensure virtualenv scripts run correctly in batch
            bat """
              @echo off
              echo Running on Windows node
              python -m venv venv
              call venv\\Scripts\\activate
              python -m pip install --upgrade pip
              if exist requirements.txt (
                pip install -r requirements.txt
              ) else (
                echo No requirements.txt
              )
            """
          }
        }
      }
    }

    stage('Run Tests') {
      steps {
        script {
          if (isUnix()) {
            sh '''
              set -e
              . venv/bin/activate
              if command -v pytest >/dev/null 2>&1; then
                pytest -q || true
              else
                echo "pytest not installed or no tests found"
              fi
            '''
          } else {
            bat '''
              @echo off
              call venv\\Scripts\\activate
              where pytest || echo pytest not found
              pytest -q || echo tests failed (continue)
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
              set -e
              . venv/bin/activate
              echo "Building/package step - replace with your real build command"
              # e.g. python setup.py sdist bdist_wheel or npm run build
              mkdir -p dist || true
              echo "Dummy artifact" > dist/README.txt
            '''
          } else {
            bat '''
              @echo off
              call venv\\Scripts\\activate
              echo Building/package step - replace with your real build command
              if not exist dist mkdir dist
              echo Dummy artifact > dist\\README.txt
            '''
          }
        }
      }
    }

    stage('Archive Artifacts') {
      steps {
        // archives whatever matches ARTIFACT_GLOB; adjust ARTIFACT_GLOB in environment as needed
        archiveArtifacts artifacts: "${env.ARTIFACT_GLOB}", allowEmptyArchive: true
      }
    }
  }

  post {
    always {
      script {
        if (isUnix()) {
          sh 'echo "Post: workspace cleanup (optional)"; ls -la || true'
        } else {
          bat '@echo off & echo Post: workspace info & dir || true'
        }
      }
    }
    success {
      echo 'Pipeline completed successfully ✅'
    }
    failure {
      echo 'Pipeline failed ❌ — check console output'
    }
  }
}
