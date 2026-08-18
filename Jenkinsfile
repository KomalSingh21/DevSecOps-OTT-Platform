```groovy
pipeline {
    agent any

    tools {
        jdk 'jdk21'
        nodejs 'nodejs20'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Clean Workspace') {
            steps { cleanWs() }
        }

        stage('Checkout from Git') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/KomalSingh21/DevSecOps-OTT-Platform.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \\
                        -Dsonar.projectName=ott \\
                        -Dsonar.projectKey=ott
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate(
                        abortPipeline: true,
                        credentialsId: 'sonar-token'
                    )
                }
            }
        }

        stage('Install Dependencies') {
            steps { sh 'npm install' }
        }

        stage('OWASP FS Scan') {
            steps {
                dependencyCheck(
                    additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit',
                    odcInstallation: 'DP-Check'
                )
                dependencyCheckPublisher(
                    pattern: '**/dependency-check-report.xml'
                )
            }
        }

        stage('Trivy FS Scan') {
            steps { sh 'trivy fs . > trivyfs.txt' }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(
                        credentialsId: 'docker',
                        toolName: 'docker'
                    ) {
                        withCredentials([
                            string(
                                credentialsId: 'tmdb-api-key',
                                variable: 'TMDB_V3_API_KEY'
                            )
                        ]) {
                            sh '''
                                docker build \\
                                --build-arg TMDB_V3_API_KEY="$TMDB_V3_API_KEY" \\
                                -t ott .
                            '''

                            sh 'docker tag ott comal21/ott:latest'
                            sh 'docker push comal21/ott:latest'
                        }
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image comal21/ott:latest > trivyimage.txt'
            }
        }

        stage('Deploy to Container') {
            steps {
                sh 'docker run -d --name ott -p 8081:80 comal21/ott:latest'
            }
        }
    }
}
        stage('Deploy to kubernetes'){
            steps{
                script{
                    dir('Kubernetes') {
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                                sh 'kubectl apply -f deployment.yml'
                                sh 'kubectl apply -f service.yml'
                        }   
                    }
                }
       

    
    post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'comalsingh12@gmail.com',                                #change mail here
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
}
}

