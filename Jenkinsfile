pipeline {
    agent any
    environment {
        // SonarQube Environment Variables
        SONARQUBE_SERVER = 'SonarQube' // Name of the SonarQube server configured in Jenkins
        MAVEN_TOOL = 'Maven'    // Name of the Maven installation configured in Jenkins
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Clone repository
                git branch: 'main', url: 'https://github.com/akhil2099/maven-demo.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                // Perform SonarQube analysis
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    sh "${tool MAVEN_TOOL}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=maven-project"
                }
            }
        }
        stage('Quality Gate') {
            steps {
                // Wait for SonarQube quality gate result
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error "Pipeline aborted due to failing quality gate: ${qualityGate.status}"
                        }
                    }
                }
            }
        }
        stage('Build Project') {
            steps {
                // Build the Maven project
                sh "${tool MAVEN_TOOL}/bin/mvn clean package"
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}

