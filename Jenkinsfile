pipeline {
    agent any

    stages {
        stage('Webhook Details') {
            steps {
                echo 'Webhook triggered successfully!'
                echo '========== WEBHOOK DETAILS =========='
                echo "Payload is ${payload}"
                echo "PR Number is ${pr_number}"
                echo "Repository is ${repository}"
                echo "Action is ${action}"
                echo '====================================='
            }
        }

        stage('Check PR Conflict') {
            steps {
                script {
                    def mergeable = null

                        withCredentials([usernamePassword(
                            credentialsId: 'github-cred',
                            usernameVariable: 'USERNAME',
                            passwordVariable: 'TOKEN'
                        )]) {
                            mergeable = sh(
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

                    if (mergeable == 'true') {
                        echo 'No conflict'
                    } else if (mergeable == 'false') {
                        echo 'Conflict detected...'

                        withCredentials([usernamePassword(
        credentialsId: 'github-cred',
        usernameVariable: 'USERNAME',
        passwordVariable: 'TOKEN'
    )]) {
                            def files = sh(
            script: """
                curl -s -L \
                  -H "Accept: application/vnd.github+json" \
                  -H "Authorization: Bearer \$TOKEN" \
                  "https://api.github.com/repos/12pavani/Webhook-Learn/pulls/${pr_number}/files"
            """,
            returnStdout: true
        ).trim()

                            echo 'Files in PR:'
                            echo files
    }
                    }
                }
            }
        }
    }
}
