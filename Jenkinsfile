pipeline {
    agent any

    environment {
        RENDER_API_KEY = credentials('render-api-key')
        RENDER_SERVICE_ID = 'srv-xxxxxxxxxx'  // replace with your Render Service ID
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . venv/bin/activate
                pytest --maxfail=1 --disable-warnings -q
                '''
            }
        }

        stage('Deploy to Render') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                sh '''
                curl -X POST "https://api.render.com/v1/services/$RENDER_SERVICE_ID/deploys" \
                -H "Accept: application/json" \
                -H "Authorization: Bearer $RENDER_API_KEY" \
                -H "Content-Type: application/json"
                '''
            }
        }
    }
}
