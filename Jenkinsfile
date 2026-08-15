pipeline {
    agent any

    stages {
        stage('Webhook Test') {
            steps {
                echo 'Webhook triggered successfully!'
                echo 'Hello from Jenkins'
                echo 'World here is nice!'
                echo "========== WEBHOOK DETAILS =========="
                echo "Payload is ${payload}"
                echo "PR Number is ${pr_number}"
                echo "Repository is ${repository}"
                echo "Action is ${action}"
                echo "====================================="
            }
        }
    }
}
