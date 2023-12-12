pipeline{
    agent any
    environment{
        SONAR_HOME= tool "sonar-scanner"
    }
    stages{
        stage("Code Checkout"){
            steps{
                git url:"https://github.com/rajeshmamuddu/Nodetodo-CI-CD-list.git", branch:"main"
            }
        }
        
        stage("SonarQube Analysis"){
            steps{
                withSonarQubeEnv("Sonar-token"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=nodetodo -Dsonar.projectKey=nodetodo"
                }
            }
        }
        
        stage("SonarQube Quality Gates"){
                steps{
                    timeout(time: 1, unit: "MINUTES"){
                        waitForQualityGate abortPipeline: false
                    }
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
                sh "docker build -t nodeapp ."
            }
        }
        
        stage("Docker Code Scan: Trivy"){
            steps{
                sh "trivy image nodeapp"
            }
        }
        stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "rajesh4851/nodeapp:latest"
        // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://docker.io', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }   
        stage("Code Deploy"){
            steps{    
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}
