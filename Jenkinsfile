pipeline {
    agent any

    environment {
        VENV_DIR = "venv"
        REPORT_DIR = "reports"
        PYTHON_EXE = "python"  // or full path if Python not on PATH
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Environment') {
            steps {
                bat """
                    %PYTHON_EXE% -m venv %VENV_DIR%
                    %VENV_DIR%\\Scripts\\python.exe -m pip install --upgrade pip
                    %VENV_DIR%\\Scripts\\python.exe -m pip install -r requirements.txt
                """
            }
        }

        stage('Run Selenium Tests') {
            steps {
                bat """
                    %VENV_DIR%\\Scripts\\python.exe -m pytest --junitxml=%REPORT_DIR%\\junit.xml --cov=./ --cov-report=xml:%REPORT_DIR%\\coverage.xml
                """
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'reports/junit.xml'
            archiveArtifacts artifacts: 'reports/**', fingerprint: true
        }
        success {
            echo "All tests passed!"
        }
        failure {
            echo "Some tests failed. Check reports and console output."
        }
    }
}
