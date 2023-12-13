pipeline{
    agent any
    environment {
        SONAR_HOME = "SONAR_HOME"
        REGISTRY_CREDENTIALS = credentials('Docker-cred')
    }
    stages{
        stage("Code Checkout"){
            steps{
                git url:"https://github.com/rajeshmamuddu/Nodetodo-CI-CD-list.git", branch:"main"
            }
        } 
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Docker Code Build"){
            steps{
                sh "docker build -t nodeapp1 ."
            }
        }
        
        stage("Docker Code Scan: Trivy"){
            steps{
                sh "trivy image nodeapp1"
            }
        }    
        stage('Deploy to kubernets'){
            steps{
                script{
                    dir('Kubernetes') {
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                                sh 'kubectl apply -f deployment.yml'
                                sh 'kubectl apply -f service.yml'
                        }   
                    }
                }
            }
        }
