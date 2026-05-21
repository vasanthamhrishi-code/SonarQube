pipeline {
    agent any

    environment {
        // Points directly to your verified DockerHub target
        IMAGE_NAME = "hrishikesh01/python-cicd-demo"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Using your specific GitHub repository target
                git branch: 'main',
                    url: 'https://github.com/vasanthamhrishi-code/SonarQube.git'
            }
        }

        stage('Verify & Setup Python') {
            steps {
                sh '''
                python3 --version
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements-dev.txt
                '''
            }
        }

        stage('Run Tests with Coverage') {
            steps {
            sh '''
            export PYTHONPATH=$WORKSPACE
            . venv/bin/activate
            pytest tests/ \
            --cov=app \
            --cov-report=xml \
            -v
            '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_SCANNER_OPTS = "-Xmx512m"
            }
            steps {
                withCredentials([string(
                    credentialsId: 'sonar-token',
                    variable: 'SONAR_TOKEN'
                )]) {
                    sh '''
                    . venv/bin/activate
                    sonar-scanner \
                      -Dsonar.projectKey=python-cicd-demo \
                      -Dsonar.sources=app \
                      -Dsonar.tests=tests \
                      -Dsonar.host.url=http://sonarqube:9000 \
                      -Dsonar.token=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Trivy Security Scan') {

        steps {

        sh '''
        trivy image \
          --severity HIGH,CRITICAL \
          --exit-code 1 \
          --no-progress \
          $IMAGE_NAME:latest
        '''
    }
}

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }
    }
}