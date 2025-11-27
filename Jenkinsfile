pipeline {
    agent any

    environment {
        VENV = "/opt/venv"       // Python Virtual Environment path
        IMAGE_NAME = "flask-ci-cd-app"   // Docker Image name
    }

    stages {

        stage('Setup Python Virtual Env') {
            steps {
                echo "🔹 Creating Virtual Environment (if missing) & Installing Dependencies"
                sh '''
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
                echo "🔹 Installing Dependencies"
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
                echo "🔹 Running Unit Tests"
                sh '''
                bash -c "
                source $VENV/bin/activate
                python3 -m unittest discover -s .
                "
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker Image"
                sh '''
                docker build -t $IMAGE_NAME:latest .
                docker images | grep $IMAGE_NAME
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "🔹 Deploying Application to Workspace Directory"
                sh '''
                mkdir -p python-app-deploy
                cp app.py python-app-deploy/
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                echo "🚀 Starting Flask Application"
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
                echo "🔍 Running Tests After Deployment"
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
        success {
            echo "🎉 PIPELINE SUCCESS — CI/CD COMPLETED!"
        }
        failure {
            echo "❌ PIPELINE FAILED — CHECK STAGE OUTPUTS!"
        }
    }
}

