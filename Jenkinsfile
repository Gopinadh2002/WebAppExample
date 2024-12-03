pipeline {
    agent any

    environment {
        // SonarQube server configuration (ensure it matches your Jenkins setup)
        SONARQUBE_SERVER = 'SonarQube'  // This should match the name configured in Jenkins
        SONAR_HOST_URL = 'http://localhost:9000'  // SonarQube server URL
        SONAR_AUTH_TOKEN = '<your-sonarqube-authentication-token>'  // Set your SonarQube token
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the project (for Maven, use this)
                    sh 'mvn clean install'
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                script {
                    // Run unit tests and generate test reports (for Maven)
                    sh 'mvn test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    // Run SonarQube analysis
                    sh '''
                        mvn sonar:sonar \
                            -Dsonar.projectKey=my-project \
                            -Dsonar.host.url=${SONAR_HOST_URL} \
                            -Dsonar.login=${SONAR_AUTH_TOKEN}
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for SonarQube analysis to complete and retrieve the quality gate status
                    timeout(time: 1, unit: 'MINUTES') {
                        def qg = waitForQualityGate()  // Blocks until the quality gate is checked
                        if (qg.status != 'OK') {
                            error "Pipeline failed due to quality gate failure: ${qg.status}"  // Fail the build if the gate fails
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build and analysis successful!"
        }
        failure {
            echo "Build failed or quality gate not met!"
        }
    }
}
