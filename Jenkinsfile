pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Code already checked out from Git'
            }
        }

        stage('Compile Java') {
            steps {
                bat '''
                if exist build rmdir /s /q build
                mkdir build
                javac -d build src\\Hello.java
                jar cfe hello.jar Hello -C build .
                '''
            }
        }

        stage('Prepare Package Directory') {
            steps {
                bat '''
                if not exist package mkdir package
                if not exist package\\usr mkdir package\\usr
                if not exist package\\usr\\local mkdir package\\usr\\local
                if not exist package\\usr\\local\\bin mkdir package\\usr\\local\\bin

                copy hello.jar package\\usr\\local\\bin\\
                '''
            }
        }

        stage('Build DEB using FPM') {
            steps {
                bat '''
                docker run --rm -v %cd%:/app -w /app ruby:3 bash -c "gem install fpm && fpm -s dir -t deb -n hello-java -v 1.0.%BUILD_NUMBER% --prefix=/ -C package ."
                '''
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: '*.deb'
        }
    }
}