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
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
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
                echo 'Deploying application...'
                sh './jenkins/scripts/deliver.sh' 
                
                script {
                    input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)' 
                }
                
                echo 'Stopping application...'
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
}
