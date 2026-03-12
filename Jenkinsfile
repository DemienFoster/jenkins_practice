pipeline {
    agent {
        label 'linux'
    }
    environment {
        APP_NAME = 'flask-app'
        VERSION = "1.0.${BUILD_NUMBER}"
        IMAGE_TAG = "${BRANCH_NAME}-${VERSION}"
    }
    stages {
        stage('Branch Info') {
            steps {
                echo "Branch name: ${env.BRANCH_NAME}"
                echo "Image tag: ${env.IMAGE_TAG}"
                echo "Building ${env.APP_NAME} on branch ${env.BRANCH_NAME}"
            }
        }
        stage('Build') {
            steps {
                dir('flask-app') {
                    sh """
                    python3 -m venv venv
                    venv/bin/python -m pip install -r requirements.txt
                    echo "Build ${env.APP_NAME}:${env.VERSION}"
                    """
                }
            }
        }
        stage('Test') {
            steps {
                dir('flask-app') {
                    sh 'venv/bin/python -m pytest test_app.py -v'
                }
            }
        }
        stage('Build Docker Image') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                    branch 'feature/*'
                }
            }
            steps {
                echo " BUILD DOCKER IMAGE"
            }
        }
        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                echo "Deploying to staging environment"
            }
        }
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Deploy to production?'
                echo "Deploy to Prod..."
            }
        }
    }
    post {
        success {
            echo "Build success: on ${BRANCH_NAME}"
        }
        failure {
            echo "Build failure on ${BRANCH_NAME}"
            script {
                if (branch == 'main') {
                    echo " Critical Error!!!"
                }
            }
        }
    }
}