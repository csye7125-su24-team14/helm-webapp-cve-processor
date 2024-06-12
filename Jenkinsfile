pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Validate Commit Messages') {
            steps {
                script {
                    def latestCommitMessage = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()

                    if (!(latestCommitMessage =~ /^(fix|feat|chore|docs|style|refactor|test|perf|build|ci|revert|version|merge|hotfix|wip): .+/)) {
                        error "The commit message does not follow the Conventional Commit format:\n${latestCommitMessage}"
                    }
                }
            }
        }

        stage('Helm Lint') {
            steps {
                script {
                    def result = sh(script: 'helm lint .', returnStatus: true)
                    if (result != 0) {
                        error "Helm linting failed"
                    }
                }
            }
        }

        stage('Helm Template') {
            steps {
                script {
                    def result = sh(script: 'helm template .', returnStatus: true)
                    if (result != 0) {
                        error "Helm template generation failed"
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'All checks passed successfully'
        }
        failure {
            echo 'One or more checks failed'
        }
    }
}