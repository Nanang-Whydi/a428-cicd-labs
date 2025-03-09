pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        message: 'Apakah Anda menyetujui deployment ke production?',
                        ok: 'Lanjutkan',
                        parameters: [
                            choice(name: 'Approval', choices: ['Yes', 'No'], description: 'Pilih Yes untuk melanjutkan deployment')
                        ]
                    )
                    if (userInput == 'No') {
                        error('Deployment dihentikan oleh user.')
                    }
                }
            }
        }
        stage('Deploy') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
                input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)' 
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
}
