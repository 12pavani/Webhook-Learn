pipeline {
    agent any

    stages {
        stage('Webhook Details') {
            steps {
                echo 'Webhook triggered successfully!'
                echo "========== WEBHOOK DETAILS =========="
                echo "Payload is ${payload}"
                echo "PR Number is ${pr_number}"
                echo "Repository is ${repository}"
                echo "Action is ${action}"
                echo "====================================="
            }
        }

        stage('Check PR Conflict') {
            steps {
                script {
                    def mergeable = null

                    retry(5) {
                        // Ask GitHub for PR status

                        echo "Mergeable: ${mergeable}"

                        if (mergeable == null) {
                            echo "GitHub is still calculating mergeability..."
                            sleep 5
                            error("Retry")
                        }
                    }

                    if (mergeable == true) {
                        echo "No conflict"
                    } else if (mergeable == false) {
                        echo "Conflict detected"
                    }
                }
            }
}
    }
}
