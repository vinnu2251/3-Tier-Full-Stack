pipeline{
        agent any

        tools{
                nodejs 'node21'
        }

        environment{
                SCANNER_HOME = tool 'sonar'
        }


        stages{
                stage('Install Package Dependencies'){
                        steps{
                            sh "npm install"
                        }
                }

                stage('Unit Tests'){
                        steps{
                            sh "npm test"
                        }
                }
            
        }

        post{
            echo "build complete"
        }
}