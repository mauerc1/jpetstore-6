pipeline {
    agent any
    stages {
        stage ('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage ('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        stage ('Deploy') {
            steps {
                sh 'mvn deploy -DskipITs'
            }
            post {
                always {
                    dependencyCheckPublisher pattern: '**/target/dependency-check-report.xml'
                    archiveArtifacts artifacts: '**/target/*.war', onlyIfSuccessful: true
                }
            }
        }
        stage ('Static Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-1') {
                    sh 'mvn sonar:sonar'
                }
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
