pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    
        stage('Deploy to Stagging') {
            when {
                branch  'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login' , passwordVariable: 'USERNAME' , usernameVariable: 'USERPASS')]) {
                    sshPublisher(
                        fallOnError: true,
                        continueOnError: false,
                        publishers: [
                            configName: 'stagging',
                            sshCredentials: [
                                username: "$USERNAME",
                                encryptedPasspharse: "$USERPASS"
                                ]
                                transfers: [
                                    sshTransfer(
                                        sourcefiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execComand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/train-schedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                    ]
                            ]
                    )
                }

            }
        }
    
    
    
    }
}
