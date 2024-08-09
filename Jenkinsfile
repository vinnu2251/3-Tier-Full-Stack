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

                stage('Trivy FS Scan'){
                        steps{
                            sh "trivy fs --format table -o fs-report.html ."
                        }
                        
                stage('SonarQube'){
                        steps{
                            withSonarQubeEnv('sonar') {
                                sh "$SCANNER_HOME/bin/sonar-scanner - Dsonar.projectKey=Campground -Dsonar.projectName=Campground"
                                }
                        }
                }

                stage('Docker Build & Tag'){
                        steps{
                            script{
                                withDockerRegistry(credentialsId: 'docker-cred',toolName: 'docker'){
                                        sh "docker build -t vinay7944/camp:latest ."
                                }
                            }
                        }
                }

                stage('Trivy Image Scan'){
                        steps{
                            sh "trivy image --format table -o fs-report.html vinay7944/camp:latest"
                        }
                }

                stage('Docker Push image'){
                        steps{
                                script{
                                        withDockerRegistry(credentialsId: 'docker-cred',toolName: 'docker'){
                                                sh "docker push vinay7944/camp:latest"
                                        } 
                                }
                        }
                }

                stage('Docker deploy to local'){
                        steps{
                            script{
                                withDockerRegistry(credentialsId: 'docker-cred',toolName: 'docker'){
                                        sh "docker run -d -p 3000:3000 vinay7944/camp:latest"
                                }
                            }
                        }
                }               
            
        }

        }

        post{
            always{
                    echo "complete"
            }
        }

}
