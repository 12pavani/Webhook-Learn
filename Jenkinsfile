pipeline {
    agent any

    stages {
        stage('Webhook Details') {
            steps {
                echo 'Webhook triggered successfully!'
                echo '========== WEBHOOK DETAILS =========='
                // echo "Payload is ${payload}"
                echo "PR Number is ${pr_number}"
                echo "Repository is ${repository}"
                echo "Action is ${action}"
                echo "mergeable is ${mergeable}"
                echo '====================================='
            }
        }

        stage('Check PR Conflict') {
            steps {
                script {
                    def check_mergeable = null
                    def response = null

                        withCredentials([usernamePassword(
                            credentialsId: 'github-cred',
                            usernameVariable: 'USERNAME',
                            passwordVariable: 'TOKEN'
                        )]) {
                            response = sh(
                                script: """ 
                                curl -s -L \
                                      -H "Accept: application/vnd.github+json" \
                                      -H "Authorization: Bearer \$TOKEN" \
                                      "https://api.github.com/repos/12pavani/Webhook-Learn/pulls/${pr_number}"
                                """
                            ).trim()
                            echo "response is ${response}"

                            check_mergeable = sh(
                                script: """
                                    curl -s -L \
                                      -H "Accept: application/vnd.github+json" \
                                      -H "Authorization: Bearer \$TOKEN" \
                                      "https://api.github.com/repos/12pavani/Webhook-Learn/pulls/${pr_number}" \
                                    | grep '"mergeable":' \
                                    | sed 's/.*"mergeable": *\\([^,]*\\).*/\\1/'
                                """,
                                returnStdout: true
                            ).trim()
                        }

                        echo "Current mergeable: ${mergeable}"

                    if (check_mergeable == 'true') {
                        echo 'No conflict'
                    } else if (check_mergeable == 'false') {
                        echo 'Conflict detected...'
                    }
                }
            }
        }
    }
}
