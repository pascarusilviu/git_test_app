pipeline {
    agent any
    environment {
        CI = 'true'
        registry = "pascarusilviu/appimg"
        registryCredential = 'dockerHub'
        dockerImage = ''
    }
    stages {
        stage('install packages') {
            steps {
                sh 'npm install'
            }
        }
        stage('test') {
            steps {
                sh 'nohup npm start &'
                sleep 11
                sh 'curl -k localhost:3000'
            }
        }
        stage('build app') {
            steps {
                sh 'npm run build'
                sh 'tar -czvf build.tar.gz build/*'
                archiveArtifacts artifacts: 'build.tar.gz', followSymlinks: false
            }
        }
        // stage('publish docker img')
        stage('build docker image'){
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('scan image'){
            steps{
                sh "bash scanimg.sh"
            }
        }
        stage('publish docker image'){
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()
                    }
                }
            }
        }
        stage('deploy image'){
            steps{
                sh 'docker run -d -p 4000:80 pascarusilviu/appimg'
                sleep 10
                sh 'curl -k localhost:4000'
            }
        }
    }
}
