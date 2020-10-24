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
    stage('Deploy to stage') {
      when {
        branch 'master'
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'userpasswd', usernameVariable: 'username')]) {

          sshPublisher(
              continueOnError: false, 
              failOnError: true,
              publishers: [
                  sshPublisherDesc(
                      configName: 'staging',
                      sshCredentials: [
                          encryptedPassphrase: "$userpasswd", 
                          username: "$username"
                          ],
                      transfers: [
                          sshTransfer(
                              execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule', 
                              remoteDirectory: '/tmp', 
                              removePrefix: 'dist',
                              sourceFiles: 'dist/trainSchedule.zip'
                            )
                        ]
                       
                    )
               ]
            )

        }
      }

    }

  }
}
