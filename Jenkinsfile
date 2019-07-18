pipeline {
    agent none

    stages {
        stage('Build') {
             agent {
                docker {
                    image 'jakemorgan/hugo:latest'
                    args '-v /tmp/public:/var/lib/jenkins/workspace/jakemorgan.io_master/site/public -u 0'
                    alwaysPull true
                }
            }
            steps {
                sh 'hugo version'
                sh 'hugo -s site'
            }
        }
        stage('Deploy') {
            agent any
            steps {
                sshagent (['jenkins-ssh']) {
                    // Remove all files in nginx folder and make sure the html file is present
                    sh 'ssh -o StrictHostKeyChecking=no jenkins@jakemorgan.io sudo rm -rf /usr/share/nginx/html'
                    sh 'ssh -o StrictHostKeyChecking=no jenkins@jakemorgan.io sudo mkdir -p /usr/share/nginx/html'
                    // Copy files into home dir
                    sh 'scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -r /tmp/public jenkins@jakemorgan.io:~/'
                    // Move files from home dir to nginx folder and delete old folder
                    sh 'ssh -o StrictHostKeyChecking=no jenkins@jakemorgan.io "sudo mv ~/public/* /usr/share/nginx/html"'
                    sh 'ssh -o StrictHostKeyChecking=no jenkins@jakemorgan.io rm -rf ~/public'
                }
                sh 'sudo rm -rf /tmp/public'
            }
        }
    }
}
