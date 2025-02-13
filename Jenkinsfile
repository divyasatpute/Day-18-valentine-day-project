pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
    }

    environment {
        SCANNER_HOME = tool 'sonar'
    }

    stages {
        stage('Git Checkout') {
    steps {
        script {
            // Perform a clean clone to ensure all files are fetched
            sh 'rm -rf *'  // Clean workspace
            git branch: 'main', 
                credentialsId: 'your-git-credentials-id', 
                url: 'https://github.com/divyasatpute/Day-18-valentine-day-project.git'
            sh 'ls -lah'  // Verify that Dockerfile is present
        }
    }
}


        stage('Trivy fs scan') {
            steps {
                sh 'trivy fs --format table -o fs.html .'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=valentine -Dsonar.projectKey=valentine \
                    -Dsonar.java.binaries=target
                    '''
                }
            }
        }

        stage('Docker Build & Tag') {
    steps {
        script {
            withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                sh 'docker build -t divyasatpute/valentine:latest . --no-cache'
            }
        }
    }
}


        

        stage('Docker Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker push divyasatpute/valentine:latest'
                    }
                }
            }
        }

        stage('Deploy to Container') {
            steps {
                sh 'docker run -d --name valentine -p 8081:80 divyasatpute/valentine:latest'
            }
        }
    }
}
