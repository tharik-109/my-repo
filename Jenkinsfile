pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube'
        GRADLE_HOME = '/usr/local/gradle'  // Update if necessary
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Code Checkout') {
            steps {
                echo "Checking out code from repository..."
                script {
                    // Ensure gradle wrapper exists
                    if (!fileExists('./gradlew')) {
                        error "Gradle wrapper 'gradlew' is missing!"
                    }
                }
            }
        }

        stage('Build Project') {
            steps {
                script {
                    echo "Running Gradle build task..."
                    sh './gradlew build'
                }
            }
        }

        stage('Run Scans in Parallel') {
            parallel {
                stage('Code Stability') {
                    steps {
                        echo "Running Code Stability Checks..."
                        script {
                            // Add your stability checks here
                            sh './gradlew check'
                        }
                    }
                }

                stage('Code Quality Analysis') {
                    steps {
                        echo "Running Code Quality Analysis with SonarQube..."
                        script {
                            // Ensure SonarQube is configured in Gradle
                            sh './gradlew sonarqube'
                        }
                    }
                }

                // Removed Code Coverage Analysis (Jacoco)
            }
        }

        stage('Generate Report') {
            steps {
                script {
                    echo "Generating Reports..."
                    // Make sure reports are generated
                    if (fileExists('build/reports')) {
                        sh 'cp build/reports/* /var/lib/jenkins/workspace/Java-CI-Pipeline/'
                    } else {
                        echo "Reports not found, skipping report generation."
                    }
                }
            }
        }

        stage('Publish Artifacts') {
            steps {
                script {
                    echo "Publishing Artifacts..."
                    // Check if the reports exist before publishing
                    if (fileExists('build/libs')) {
                        // Add artifact publishing logic here
                        archiveArtifacts artifacts: 'build/libs/*.jar', allowEmptyArchive: true
                    } else {
                        echo "No artifacts found to publish."
                    }
                }
            }
        }

        stage('Approval Before Release') {
            steps {
                script {
                    echo "Waiting for approval before release..."
                    input message: 'Approve Release?', ok: 'Approve', submitter: 'admin'  // Update approver as necessary
                }
            }
        }

        stage('Notify Users') {
            steps {
                script {
                    echo "Sending Notifications..."
                    // Add your Slack or Email notification logic here
                    slackSend(channel: '#general', message: "Build completed successfully!")
                    // If using email notifications, configure as needed
                    emailext subject: 'Build Status', body: 'The build has been completed!', to: 'mtharik121@gmail.com'
                }
            }
        }
    }

    post {
        failure {
            echo "Build failed! Sending failure notifications..."
            // Send failure notifications (Slack, Email, etc.)
            slackSend(channel: '#jenkins-notification', message: "Build failed!")
            emailext subject: 'Build Failed', body: 'The build failed. Please check the Jenkins logs for more details.', to: 'mtharik121@gmail.com'
        }
        success {
            echo "Build completed successfully!"
            // Send success notifications
            slackSend(channel: '#jenkins-notification', message: "Build succeeded!")
            emailext subject: 'Build Success', body: 'The build has completed successfully!', to: 'mtharik121@gmail.com'
        }
    }
}
