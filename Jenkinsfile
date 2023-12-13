pipeline{
    agent any
    environment {
        SONAR_HOME = "SONAR_HOME"
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
        REGISTRY_CREDENTIALS = credentials('Docker-cred')
      }
      steps {
        script {
            sh 'docker build -t ${DOCKER_IMAGE} .'
                }

    withCredentials([string(credentialsId: 'Docker-cred', variable: 'PASSWORD')]) {
        sh 'docker login -u rajesh4851 -p $PASSWORD'
    }
    stage("Push Image to Docker Hub"){
        sh 'docker push rajesh4851/node-todo-list:latest'
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
