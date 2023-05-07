pipeline {
    agent any
    stages {
        stage('Send Approval Request') {
            steps {
                script {
                    // Send an email with a link to the build and a form for user input
                    def emailSubject = "Build Approval Request"
                    def emailBody = """
                        <p>Please review the build below to approve or deny</p>
                        <a href="http://65.2.175.161:8080/job/input_from_user/${env.BUILD_NUMBER}/input/">Open Jenkins</a>
                    """
                    emailext body: emailBody,
                    subject: emailSubject,
                    to: "jeeva.duraisamy1@tcs.com",
                    replyTo: env.EMAIL_USER,
                    mimeType: 'text/html'
                }
            }
        }
        stage('Wait for User Approval') {
            steps {
                script {
                    // Wait for user input
                    def userInput = input message: 'Please approve the build', parameters: [booleanParam(defaultValue: true, description: '', name: 'approval')]
                    if (userInput) {
                        echo 'User approved the build'
                    } else {
                        echo 'User rejected the build'
                        currentBuild.result = 'ABORTED'
                        error 'Build aborted by user'
                    }
                }
            }
        }
        stage('Rollback') {
            steps {
                input(message: 'Enter the build number to rollback to:', parameters: [
                    string(name: 'rollbackBuildNumber', defaultValue: '', description: 'Build number')
                ])
                
                script {
                    def rollbackBuildNumber = "${params.rollbackBuildNumber}"
                    echo "Rolling back to build number: ${rollbackBuildNumber}"
                    
                    def rollbackJobName = 'YourRollbackJobName'
                    sh "curl -X POST 'http://13.235.241.244:8080/job/${rollbackJobName}/buildWithParameters?BUILD_NUMBER=${rollbackBuildNumber}'"
                    
                }
            }
        }
    }
    post {
        success {
            emailext attachLog: true, body: 'The build was successful', subject: 'Status of Jenkins Build', to: 'jeeva.duraisamy1@tcs.com'
        }
        failure {
            emailext attachLog: true, body: 'The build was failed', subject: 'Status of Jenkins Build', to: 'jeeva.duraisamy1@tcs.com'                
        }
        aborted {
            emailext attachLog: true, body: 'The build was failed', subject: 'Status of Jenkins Build', to: 'jeeva.duraisamy1@tcs.com'                
        }
        
    }
}  
