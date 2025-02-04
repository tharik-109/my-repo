pipeline {
    agent any

    parameters {
        booleanParam(name: 'SKIP_STABILITY', defaultValue: false, description: 'Skip Code Stability Check')
        booleanParam(name: 'SKIP_QUALITY', defaultValue: false, description: 'Skip Code Quality Analysis')
        booleanParam(name: 'SKIP_COVERAGE', defaultValue: false, description: 'Skip Code Coverage Analysis')
    }

    stages {
        stage('Code Checkout') {
            steps {
                echo "Checking out code from repository..."
                checkout scm
            }
        }

        stage('Run Scans in Parallel') {
            parallel {
                stage('Code Stability') {
                    when {
                        expression { return !params.SKIP_STABILITY }
                    }
                    steps {
                        echo "Running Code Stability Checks..."
                        sh './gradlew check' // Use `mvn verify` if using Maven
                    }
                }
                
                stage('Code Quality Analysis') {
                    when {
                        expression { return !params.SKIP_QUALITY }
                    }
                    steps {
                        echo "Running Code Quality Analysis with SonarQube..."
                        sh './gradlew sonarqube' // Use `mvn sonar:sonar` if using Maven
                    }
                }

                stage('Code Coverage Analysis') {
                    when {
                        expression { return !params.SKIP_COVERAGE }
                    }
                    steps {
                        echo "Running Code Coverage Analysis..."
                        sh './gradlew jacocoTestReport' // Use `mvn jacoco:report` if using Maven
                    }
                }
            }
        }

        stage('Generate Report') {
            steps {
                echo "Generating Reports..."
                sh 'cp build/reports/* $WORKSPACE/'  // Modify path if using Maven
                archiveArtifacts artifacts: 'build/reports/**', fingerprint: true
            }
        }

        stage('Publish Artifacts') {
            steps {
                echo "Publishing Artifacts..."
                sh './gradlew build publish' // Use `mvn deploy` if using Maven
            }
        }

        stage('Approval Before Release') {
            steps {
                script {
                    def userInput = input(
                        id: 'UserApproval',
                        message: 'Approve the release?',
                        parameters: [
                            choice(name: 'Approval', choices: ['Approve', 'Deny'], description: 'Select Approve to proceed')
                        ]
                    )
                    
                    if (userInput == 'Deny') {
                        error("Publication Denied by User")
                    }
                }
            }
        }

        stage('Notify Users') {
            steps {
                echo "Sending Notifications..."
                script {
                    def jobStatus = currentBuild.result ?: 'SUCCESS'
                    slackSend(channel: '#jenkins-notification', message: "Build ${jobStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
                    emailext subject: "Build ${jobStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                             body: "Check Jenkins console for details.",
                             to: "mtharik121@gmail.com"
                }
            }
        }
    }
}

