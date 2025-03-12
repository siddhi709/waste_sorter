pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'  // Virtual environment directory
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/siddhi709/waste_sorter.git', branch: 'main'
            }
        }

        stage('Verify Python Installation') {
            steps {
                sh "python3 --version"
            }
        }

        stage('Set Up Virtual Environment') {
            steps {
                sh '''
                if [ ! -d "$VENV_DIR" ]; then
                    python3 -m venv $VENV_DIR
                fi
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                source $VENV_DIR/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                deactivate
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                source $VENV_DIR/bin/activate
                pytest tests/ --maxfail=1 --disable-warnings -q
                deactivate
                '''
            }
        }

        stage('Deploy Flask App') {
            steps {
                sh '''
                source $VENV_DIR/bin/activate
                nohup gunicorn -w 4 -b 0.0.0.0:5000 app:app > flask.log 2>&1 &
                echo $! > flask_pid.txt
                deactivate
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo '✅ Build, tests, and deployment completed successfully!'
        }
        failure {
            echo '❌ Build or tests failed. Check the logs for details.'
        }
    }
}
