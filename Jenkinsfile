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
    }
}