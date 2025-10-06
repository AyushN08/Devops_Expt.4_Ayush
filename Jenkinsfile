pipeline {
    agent any

    environment {
        VENV_DIR = "venv"
        REPORT_DIR = "reports"
        PYTHON = "python3"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Environment') {
            steps {
                bat '''
                    set -e
                    ${PYTHON} -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Selenium Tests') {
            steps {
                bat '''
                    set -e
                    . ${VENV_DIR}/bin/activate
                    mkdir -p ${REPORT_DIR}
                    pytest --junitxml=${REPORT_DIR}/junit.xml --cov=./ --cov-report=xml:${REPORT_DIR}/coverage.xml
                '''
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
