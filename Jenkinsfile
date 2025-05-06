pipeline {
    agent {
        docker {
            image 'python:3.10'
        }
    }

    environment {
        VENV = 'venv'
    }

    stages {
        stage('Setup Environment & Install Dependencies') {
            steps {
                bat '''
                    python -m venv %VENV%
                    call %VENV%\\Scripts\\activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                    call %VENV%\\Scripts\\activate
                    pytest test_app.py
                '''
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/.*", comparator: "REGEXP"
                }
            }
            steps {
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://ptb.discord.com/api/webhooks/1369232517668012072/n1mNh2Arwd8mJTOogXXqxvrk2WVwKggUzTE7iXWVO70rFCIGJMATJqO70mIhgw8FCjZ4'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://ptb.discord.com/api/webhooks/1369232517668012072/n1mNh2Arwd8mJTOogXXqxvrk2WVwKggUzTE7iXWVO70rFCIGJMATJqO70mIhgw8FCjZ4'
                )
            }
        }
    }
}
