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
                echo "Head branch is ${head_branch}"
                echo "Base branch is ${base_branch}"
                echo "Action is ${action}"
                echo "Start"
                echo '====================================='
            }
        }

        stage('Checkout') {
            steps {
                script {

                    withCredentials([usernamePassword(
                        credentialsId: 'github-cred',
                        usernameVariable: 'USERNAME',
                        passwordVariable: 'TOKEN'
                    )]) {

                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: "*/${head_branch}"]],
                            userRemoteConfigs: [[
                                url: "https://github.com/12pavani/Webhook-Learn.git",
                                credentialsId: 'github-cred'
                            ]]
                        ])
                    }
                }
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

                        echo 'Conflict Detected'

                        /*
                         * This API gives all files modified by the PR.
                         * It does NOT give only the conflicted files.
                         *
                         * Keeping this code for future reference.
                         */

                        /*
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

                            echo 'Files in PR (Only Modified files):'
                            echo files
                        }
                        */

                        echo '========== GETTING CONFLICTED FILES =========='

                    sh """
                        echo "Head branch is ${head_branch}"
                        echo "Base branch is ${base_branch}"

                        git fetch origin ${base_branch}

                        git checkout -B conflict-check origin/${head_branch}

                        git merge origin/${base_branch} || true

                        echo "========== CONFLICTED FILES =========="

                        git diff --name-only --diff-filter=U

                        echo "======================================" 
                        """

                    }
                }
            }
        }
        }
    }
