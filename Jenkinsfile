pipeline {
    agent any
    tools {
        dotnetsdk 'dotnet5'
    }
    stages {
    	stage('Build') 	{
                steps {
                        sh 'dotnet publish src/weathy.csproj -c release'
                }
    	}
    	stage('Archival') {
                steps {
                        archiveArtifacts 'bin/release/net5.0/publish/*.dll'
                }
    	}
        /*stage('Test cases') {
                steps {
                        sh "dotnet test test/weathy-test.csproj"
                        //step([$class: 'MSTestPublisher', testResultsFile:"test/bin/Debug/net5.0/Weathy.xml", failOnError: true, keepLongStdio: true])
                        //mstest testResultsFile:"***.xml", keepLongStdio: true
                        junit 'temporary-junit-reports/*.xml'
                }
        }*/
        stage('Build Image') {
            steps {
                sh '''
                    docker build --no-cache -t weathy-image:latest .
                    docker tag weathy-image:latest akmaharshi/weathy-image:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker login --username akmaharshi --password sairam123
                    docker push akmaharshi/weathy-image:v${BUILD_NUMBER}
                '''
            }
        }
    }
    
    post {
        always {
            notify('started')
        }
        failure {
            notify('err')
        }
        success {
            notify('success')
        }
    }    
}

def notify(status){
    emailext (
    to: "devops.kphb@gmail.com",
    subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
    body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME}  [${env.BUILD_NUMBER}]</a></p>""",
    )
}
