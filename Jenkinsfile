pipeline {
    agent any

    stages {
        stage("load lib") {
            agent { label 'master' }
            steps {
                script {
                    sh '''
                        cd library && \
                        (rm -rf .git || true) && \
                        git config --global user.email "you@example.com" && \
                        git config --global user.name "Your Name" && \
                        git init && \
                        git add --all && \
                        git commit -m init
                    '''

                    def repoPath = sh(returnStdout: true, script: 'pwd').trim() + "/library"

                    echo "REPO PATH: $repoPath"

                    library identifier: 'local-lib@master',
                            retriever: modernSCM([$class: 'GitSCMSource', remote: "$repoPath"]),
                            changelog: false

                    deleteDir() // After loading the library we can clean the workspace
                    echo "Done loading shared library"
                }
            }
        }

        stage("Test") {
            agent { label 'docker' }
            steps {
                localTest()
            }
        }
    }
}