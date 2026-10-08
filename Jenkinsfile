pipeline {
    agent none

    options {
        timestamps()
        timeout(time: 15, unit: 'MINUTES')
    }

    stages {

        stage('Where am I (Controller)') {
            agent {
                label 'built-in'
            }

            steps {
                echo "NODE = ${env.NODE_NAME}"
                echo "WORKSPACE = ${env.WORKSPACE}"
            }
        }

        stage('Build on Windows Agent') {
            agent {
                label 'win'
            }

            steps {
                bat """
                    if exist out rmdir /s /q out
                    mkdir out

                    javac -d out Calculator.java
                    java -cp out Calculator

                    echo Build_OK > artifact.txt
                """
            }

            post {
                always {
                    archiveArtifacts artifacts: 'artifact.txt, out/**',
                        allowEmptyArchive: false
                }
            }
        }

        stage('Parallel: Controller vs Agent') {
            parallel {

                stage('Controller lane') {
                    agent {
                        label 'built-in'
                    }

                    steps {
                        echo "PARALLEL NODE = ${env.NODE_NAME}"
                    }
                }

                stage('Agent lane') {
                    agent {
                        label 'win'
                    }

                    steps {
                        bat "echo PARALLEL NODE = %NODE_NAME%"
                    }
                }
            }
        }
    }
}