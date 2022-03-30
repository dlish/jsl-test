pipeline {
	agent any

	stages {
        stage("load lib") {
            agent { label 'master' }
            steps {
                checkout scm

                script {
                    sh '''
                        cd ./library && \
                        (rm -rf .git || true) && \
                        git init && \
                        git add --all && \
                        git commit -m init
                    '''

                    def repoPath = sh(returnStdout: true, script: 'pwd').trim() + "/$libraryPath"

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