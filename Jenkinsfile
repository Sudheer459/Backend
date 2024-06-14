pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('Xterm')        
    }
    environment{
        def appVersion = '' //variable declaration
        nexusUrl = 'nexus.sudheer459.online:8081'
    }
    stages {
        stage('read the version'){
            steps{
                script{
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "application version: $appVersion"
                }
                
            }
        }
        stage('Install Dependencies') {
            steps {
                sh """
                npm install
                ls -ltr
                echo "application version: $appVersion"
                """
            }
        }
        stage('Build'){
            steps{
                sh """
                zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                ls -ltr
                """
            }
        }

        stage('MNexus Artifact Upload'){
            steps{
                script{
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${AppVersion}",
                        repository: 'Backend',
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: "Backend",
                            classifier: '',
                            file: "Backend-" + "${appVersion}" + '.zip',
                            type: 'zip']
                        ]
                    )

                }
            }
        }
        stage('Deploy'){
            steps{
                script{
                    build job: 'backend-deploy', parameters: [string(name: 'appVersion', value: 'stage')], propagate: false
                }
            }
        }
    }   
    post {
        always {
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success {
            echo 'I will run when pipeline is success'
        }
        failure {
            echo 'I will run when pipeline is failures'
        }
    }
}