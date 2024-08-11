pipeline{
        agent any

        tools{
                nodejs 'node21'
        }

        environment{
                SCANNER_HOME = tool 'sonar-scanner'
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
                }
                        
                stage('SonarQube'){
                        steps{
                            withSonarQubeEnv('sonar') {
                                sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Campground -Dsonar.projectName=Campground"
                                }
                        }
                }

                stage('Docker Build & Tag') {
                        steps {

                                script {
                                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') 
                                        {
                                                sh 'docker build -t vinay7944/camp:latest .'
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

                stage('Docker to EKS'){
                        steps{
                                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: ' my-eks-cluster1', contextName: '', credentialsId: 'kube-token', namespace: 'webapps', serverUrl: 'https://CCD283B31AEA0E7BB2594FE427AF5D9C.sk1.ap-south-1.eks.amazonaws.com']]) {

                                                sh "kubectl apply -f Manifests/"
                                                sleep 60
                                        }
                          
                        }
                }

                 stage('Verify the Deployment'){
                        steps{
                                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: ' my-eks-cluster1', contextName: '', credentialsId: 'kube-token', namespace: 'webapps', serverUrl: 'https://CCD283B31AEA0E7BB2594FE427AF5D9C.sk1.ap-south-1.eks.amazonaws.com']]) {
                                        
                                                sh "kubectl get pods -n webapps"
                                                sh "kubectl get svc -n webapps"
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
