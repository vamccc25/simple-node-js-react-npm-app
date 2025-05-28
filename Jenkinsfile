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
         stage('Quality Gate'){
        steps {
            timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
        }

    }

stage('Upload Artifact to Nexus') {

    environment {
    NEXUS_USERNAME = credentials('nexus-creds')
    NEXUS_PASSWORD = credentials('nexus-creds')
}

    steps {
        script {
            // Example: package.json contains version like "1.0.0-SNAPSHOT"
            def packagejson = readjSON file: 'package.json'
            def version = packagejson.version
            def artifactId = packagejson.name

            // Generate .tgz package using npm pack
            sh "npm pack"
            def tarball = "${artifactId}-${version}.tgz"

            // Choose Nexus repository
            def repository = version.contains('SNAPSHOT') ? 'npm-snapshots' : 'npm-releases'

            // Upload .tgz to Nexus (use raw repo or npm-compatible repo with curl)
            sh """
                curl -v -u \$NEXUS_USERNAME:\$NEXUS_PASSWORD --upload-file ${tarball} \
                http://nexus:8081/repository/${repository}/${artifactId}/${version}/${tarball}
            """
        }
    }
 }
 }
}

    