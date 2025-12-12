pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    environment {
        NODE_ENV = 'test'
        SONAR_HOST_URL = 'http://sonarqube:9000'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Compile TypeScript') {
            steps {
                echo 'Compiling TypeScript...'
                sh 'npm run compile'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                sh 'npm test -- --coverage'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                withEnv(['SONAR_HOST_URL=http://sonarqube:9000', 'SONAR_LOGIN=sqa_41c78a24443317373fe547c87f28abd5d3399091']) {
                    sh '''
                        npx sonar-scanner \
                            -Dsonar.projectKey=Bank \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=$SONAR_HOST_URL \
                            -Dsonar.login=$SONAR_LOGIN \
                            -Dsonar.tests=__testes__ \
                            -Dsonar.testExecutionReportPaths=test-report.xml \
                            -Dsonar.test.inclusions=**/*.test.ts \
                            -Dsonar.exclusions=coverage/lcov-report/**/*.*,node_modules/**/*.*,jest.config.js,reports/**/*.*,features/**/*.*,cucumber.js \
                            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed'
            junit testResults: 'test-report.xml', skipPublishingChecks: true
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
