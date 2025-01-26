pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    tools {
        maven 'maven3'
        jdk 'jdk11'
        jdk 'jdk17'
    }

    stages {
        // stage('Git Checkout') {
        //     steps {
        //         git 'https://github.com/shubham-sihasane/Greetings.git'
        //     }
        // }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName-MavenProject -Dsonar.projectKey=MavenProject -Dsonar.java.binaries=.
                        echo $SCANNER_HOME
                    '''
                }
            }
        }
        stage('SonarQuality') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
         stage('Deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'Tomcat-Deployer', path: '', url: 'http://13.233.100.223:8080')], contextPath: null, onFailure: false, war: '**/*.war'
            }
        }
    }
}
