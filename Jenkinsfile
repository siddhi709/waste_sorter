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
            # Ensure Python is installed
            if ! command -v python3 &> /dev/null; then
                echo "Python3 not found, install it first!"
                exit 1
            fi

            # Create virtual environment only if it doesn't exist
            if [ ! -d "venv" ]; then
                python3 -m venv venv || { echo "Failed to create virtual environment"; exit 1; }
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
