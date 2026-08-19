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

                    retry(5) {
                        withCredentials([string(credentialsId: 'github-cred', variable: 'GITHUB_TOKEN')]) {
                            def response = sh(
        script: """
            curl -s -L \
              -H "Accept: application/vnd.github+json" \
              -H "Authorization: Bearer $GITHUB_TOKEN" \
              "https://api.github.com/repos/12pavani/Webhook-Learn/pulls/${pr_number}"
        """,
        returnStdout: true
    ).trim()

                            echo response
                        }

                        mergeable = readJSON text: response

                        echo "Current mergeable: ${mergeable.mergeable}"

                        if (mergeable.mergeable == null) {
                            echo 'GitHub is still calculating mergeability...'
                            sleep 5
                            error('Retry')
                        }
                    }

                    if (mergeable == true) {
                        echo 'No conflict'
                    } else if (mergeable == false) {
                        echo 'Conflict detected'
                    }
                }
            }
        }
    }
}
