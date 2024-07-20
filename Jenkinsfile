pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES') 
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment {
        def appVersion = '' //variable declaration
        //nexusUrl = 'nexus.devopslearning2025.online:8081'   
        nexusUrl = 'nexus.devopslearning2025.online:8081'
    }
    stages {
        stage('read the version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "APplication version : $appVersion"
                }
            }
        }
        stage('build') {
            steps {
                sh """
                zip -q -r frontend-${appVersion}.zip * -x Jenkinsfile -x frontend-${appVersion}.zip
                ls -lrth
                """
            }
        }    
        stage('Nexus Artifact upload') {
            steps {
                script {
                    nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: "${nexusUrl}",
                            groupId: 'com.expense',
                            version: "${appVersion}",
                            repository: 'frontend',
                            credentialsId: 'nexus-auth',
                            artifacts: [
                                [artifactId: "frontend",
                                classifier: '',
                                file: "frontend-" + "${appVersion}" + ".zip",
                                type: 'zip']
                            ]
                    )
                }
            }
       }
        stage('deploy') {
            steps {
                script{
                    def params = [
                        string(name: 'appVersion', value: "${appVersion}")
                    ]
                    build job: 'frontend-deploy' , parameeters: params , wait: false
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
            echo 'i will run the pipeline is usccess'
        }
        failure {
            echo 'i will the pipeline is failure'
        }
    }
}