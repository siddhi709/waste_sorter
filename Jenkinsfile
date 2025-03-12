pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
        PYTHON_PATH = 'python3'  // Assuming Python 3 is installed
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/siddhi709/waste_sorter.git', branch: 'main'
            }
        }

        stage('Set Up Virtual Environment') {
            steps {
                script {
                    sh '''
                    if [ ! -d "$VENV_DIR" ]; then
                        $PYTHON_PATH -m venv $VENV_DIR
                    fi
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh '''
                    source $VENV_DIR/bin/activate
                    pip install -r requirements.txt
                    deactivate
                    '''
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh '''
                    source $VENV_DIR/bin/activate
                    pytest tests/ --maxfail=1 --disable-warnings -q
                    deactivate
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build and tests passed successfully!'
        }
        failure {
            echo 'Build or tests failed. Please check the logs.'
        }
    }
}
