pipeline {
    agent any

    environment {
        // Define environment variables for SonarQube
        SONARQUBE_SERVER = 'SonarQube' // This should match your SonarQube server configuration in Jenkins
        SONAR_HOST_URL = 'http://localhost:9000' // SonarQube URL
        SONAR_AUTH_TOKEN = 'sqp_a2192a8909f0ea589348e097cc02cbdb5c5318e0' // Your SonarQube authentication token
        PROJECT_KEY = 'my-project' // Your SonarQube project key
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Checkout the code from the source control repository
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the project (For Maven, use mvn clean install)
                    sh 'mvn clean install'
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                script {
                    // Run unit tests (For Maven, this command runs unit tests and generates reports)
                    sh 'mvn test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    // Run SonarQube analysis with Maven
                    sh '''
                        mvn sonar:sonar \
                            -Dsonar.projectKey=${PROJECT_KEY} \
                            -Dsonar.host.url=${SONAR_HOST_URL} \
                            -Dsonar.login=${SONAR_AUTH_TOKEN}
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for the quality gate status from SonarQube
                    timeout(time: 1, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline failed due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build and quality gate passed!"
        }
        failure {
            echo "Build failed or quality gate not met!"
        }
    }
}
