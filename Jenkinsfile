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

    }
}