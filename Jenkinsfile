pipeline {
    agent any

    environment {
        SKIP_CODE_STABILITY = "${params.SKIP_CODE_STABILITY}"
        SKIP_QUALITY_ANALYSIS = "${params.SKIP_QUALITY_ANALYSIS}"
        SKIP_COVERAGE_ANALYSIS = "${params.SKIP_COVERAGE_ANALYSIS}"
    }

    parameters {
        booleanParam(name: 'SKIP_CODE_STABILITY', defaultValue: false, description: 'Skip Code Stability Check')
        booleanParam(name: 'SKIP_QUALITY_ANALYSIS', defaultValue: false, description: 'Skip Code Quality Analysis')
        booleanParam(name: 'SKIP_COVERAGE_ANALYSIS', defaultValue: false, description: 'Skip Code Coverage Analysis')
    }

    stages {
        stage('Code Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tharik-109/DemoProject.git'
            }
        }

        stage('Parallel Scans') {
            parallel {
                stage('Code Stability') {
                    when { expression { return SKIP_CODE_STABILITY.toBoolean() == false } }
                    steps {
                        sh 'mvn clean compile'
                    }
                }
                stage('Code Quality Analysis') {
                    when { expression { return SKIP_QUALITY_ANALYSIS.toBoolean() == false } }
                    steps {
                        sh 'mvn spotbugs:check'
                    }
                }
            }
        }

        stage('Generate Report') {
            steps {
                sh 'mvn site'
                archiveArtifacts artifacts: '**/target/site/*', fingerprint: true
            }
        }

        stage('Publish Artifacts') {
            steps {
                sh 'mvn package'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }

        stage('Approval Before Release') {
            steps {
                script {
                    def userInput = input(message: 'Approve Release?', parameters: [booleanParam(defaultValue: false, description: 'Approve Release?', name: 'APPROVE')])
                    if (userInput) {
                        echo "Release Approved. Proceeding with publishing..."
                    } else {
                        error "Release Denied!"
                    }
                }
            }
        }

        stage('Send Notifications') {
            steps {
                script {
                    slackSend channel: 'jenkins-notification', message: "Pipeline execution completed successfully."
                    emailext subject: "Pipeline Execution Status",
                             body: "Pipeline execution completed successfully.",
                             to: "mtharik121@gmail.com"
                }
            }
        }
    }
}
