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
                sh 'python3 --version'
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
                pip install -r requirements.txt
                deactivate
                '''
            }
        }

        stage('Start Flask App') {
            steps {
                sh '''
                source $VENV_DIR/bin/activate
                nohup python3 -m flask run --host=0.0.0.0 --port=5000 > flask.log 2>&1 &
                echo $! > flask_pid.txt
                deactivate
                '''
            }
        }

        stage('Wait for Flask to Start') {
            steps {
                sh '''
                until curl -s http://localhost:5000 > /dev/null; do
                    echo "Waiting for Flask to start..."
                    sleep 5
                done
                echo "Flask is up and running!"
                '''
            }
        }

        stage('Run Selenium Tests') {
            steps {
                sh '''
                source $VENV_DIR/bin/activate
                pytest test_selenium.py --maxfail=1 --disable-warnings -q
                deactivate
                '''
            }
        }

        stage('Stop Flask App') {
            steps {
                sh '''
                if [ -f flask_pid.txt ]; then
                    kill $(cat flask_pid.txt)
                    rm flask_pid.txt
                fi
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo '✅ Build and tests passed successfully!'
        }
        failure {
            echo '❌ Build or tests failed. Please check the logs.'
        }
    }
}
