pipeline {
    agent any

    environment {
        VENV = "/opt/venv"    // Path of virtual environment created in Jenkins container
    }

    stages {

        stage('Setup Python Virtual Env') {
            steps {
                echo "Activating Python venv..."
                sh """
                if [ ! -d "$VENV" ]; then
                    python3 -m venv $VENV
                fi
                source $VENV/bin/activate
                pip install --upgrade pip --break-system-packages
                pip install -r requirements.txt --break-system-packages
                """
            }
        }

        stage('Build') {
            steps {
                echo "Installing dependencies..."
                sh """
                source $VENV/bin/activate
                pip install -r requirements.txt --break-system-packages
                """
            }
        }

        stage('Unit Test') {
            steps {
                echo "Running unit tests..."
                sh """
                source $VENV/bin/activate
                python3 -m unittest discover -s .
                """
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application..."
                sh """
                mkdir -p python-app-deploy
                cp app.py python-app-deploy/
                """
            }
        }

        stage('Run Flask App') {
            steps {
                echo "Starting Flask application in background..."
                sh """
                source $VENV/bin/activate
                nohup python3 python-app-deploy/app.py > app.log 2>&1 &
                echo \$! > app.pid
                """
            }
        }

        stage('Post-Deployment Test') {
            steps {
                echo "Re-running tests after deployment..."
                sh """
                source $VENV/bin/activate
                python3 test_app.py
                """
            }
        }
    }

    post {
        success {
            echo "🚀 Pipeline Finished Successfully! All stages passed."
        }
        failure {
            echo "❌ Pipeline Failed. Check logs."
        }
    }
}
