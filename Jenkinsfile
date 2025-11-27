pipeline {
    agent any

    environment {
        VENV = "/opt/venv"   // venv created earlier inside Jenkins docker
    }

    stages {

        stage('Setup Python Virtual Env') {
            steps {
                echo "Creating & Activating VENV..."
                sh '''
                # Use bash explicitly so 'source' works
                bash -c "
                if [ ! -d $VENV ]; then
                    python3 -m venv $VENV
                fi
                source $VENV/bin/activate
                pip install --upgrade pip --break-system-packages
                pip install -r requirements.txt --break-system-packages
                "
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Installing dependencies in venv..."
                sh '''
                bash -c "
                source $VENV/bin/activate
                pip install -r requirements.txt --break-system-packages
                "
                '''
            }
        }

        stage('Unit Test') {
            steps {
                echo "Running unit tests..."
                sh '''
                bash -c "
                source $VENV/bin/activate
                python3 -m unittest discover -s .
                "
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application..."
                sh '''
                mkdir -p python-app-deploy
                cp app.py python-app-deploy/
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                echo "Launching Flask in background..."
                sh '''
                bash -c "
                source $VENV/bin/activate
                nohup python3 python-app-deploy/app.py > app.log 2>&1 &
                echo $! > app.pid
                "
                '''
            }
        }

        stage('Post-Deployment Test') {
            steps {
                echo "Testing Application After Deployment..."
                sh '''
                bash -c "
                source $VENV/bin/activate
                python3 test_app.py
                "
                '''
            }
        }
    }

    post {
        success { echo "🚀 Pipeline Completed Successfully! All Stages Passed." }
        failure { echo "❌ Pipeline Failed — Fix above errors." }
    }
}

