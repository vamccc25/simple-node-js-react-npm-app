pipeline {
    agent any


    stages {

        stage('install  os deps') {
            steps {
                sh '''
    curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
    apt-get install -y nodejs
    '''

            }
        }

        stage('install node deps') {
            steps {
                sh 'npm install'
            }
        }

          stage('Install Test Reporter') {
            steps {
                sh 'npm install --save-dev jest-junit'
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test -- --ci --reporters=default --reporters=jest-junit'
            }
        }
        stage('Publish Test Results') {
            steps {
                junit 'junit.xml'
            }
        }
        stage("CodeScanning"){
              environment {
           SONAR_HOME = tool name: 'sonar-scan'
        }
        steps {    
              
           withSonarQubeEnv('sonar-qube') {
                sh '''$SONAR_HOME/bin/sonar-scanner \
                       -Dsonar.projectKey=node \
                       -Dsonar.projectName=node \
                       -Dsonar.sources=src/ \
                       -Dsonar.analysis.mode=publish \
                       -Dsonar.exclusions=**/*.html \
                       -Dsonar.projectVersion=${BUILD_NUMBER}
                
                '''
            }
       }

    }
}
}
    