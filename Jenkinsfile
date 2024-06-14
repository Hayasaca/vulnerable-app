pipeline {
    agent any

    environment {
        ZAP_DOCKER_IMAGE = 'ictu/zap2docker-weekly'
        TARGET_URL = 'http://192.168.23.156:6060/login'
        CONTEXT_FILE = 'Dast_Auth.context'
        AUTH_SCRIPT = 'Long'
        REPORT_FILE = 'Authedreport.html'
        SUDO_PASSWORD = 'kali'
    }
    
    stages {
        stage('Build Jobs'){
            steps{
                script{
                    sh "java -javaagent:./contrast-agent.jar -Dcontrast.config.path=contrast_security.yaml -jar vulnapp-0.0.1-SNAPSHOT.jar --server.port=6060 &"                
                }
            }
        }
        stage('Pull ZAP Docker Image') {
            steps {
                script {
                    sh "sleep 25s"
                    sh "curl -X POST http://192.168.23.156:6060/register -d \"email=Long\" -d \"password=Long\" -d \"fullName=Long\" "
                    // Pull the ZAP Docker image
                    sh "sudo docker pull ${ZAP_DOCKER_IMAGE}"
                }
            }
        }

        stage('Run ZAP Full Scan') {
            steps {
                script {
                    try{
                    // Run the ZAP full scan in Docker, using the context file from /home/kali/Desktop
                        sh '''
                        sudo docker run --net host -v $(pwd):/zap/wrk/:rw -t ${ZAP_DOCKER_IMAGE} \
                        zap-full-scan.py -t ${TARGET_URL} -r ${REPORT_FILE}
                        '''
                        
                    }
                    catch (Exception e){
                        
                    }
                }
            }
        }

        stage('Find path') {
            steps {
                script {
                    sh "pwd"
                }
            }
        }

        //stage('Archive ZAP Report') {
        //    steps {
        //        script {
        //            // Archive the ZAP report in Jenkins
        //           archiveArtifacts artifacts: 'Authedreport.html', allowEmptyArchive: true
        //        }
        //    }
        //}
    }

    post {
        success {
            emailext body: "Build ${currentBuild.fullDisplayName} succeeded",
            subject: "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - Successful",
	    attachmentsPattern: "${REPORT_FILE}",
            to: '21520090@gm.uit.edu.vn',
	    attachLog: true
	}
	failure {
            emailext body: "Build ${currentBuild.fullDisplayName} failed",
            subject: "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - Failed",
            to: '21520090@gm.uit.edu.vn',
            attachLog: true
        }
    }

    //post {
    //    always {
    //        // Clean up actions, such as removing temporary files
    //        cleanWs()
    //    }
    //}
}
