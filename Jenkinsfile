pipeline {
    agent any

    environment {
        IMAGE = 'ruby:3.1'
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Code already checked out from Git'
            }
        }

        stage('Setup Environment') {
            steps {
                script {
                    docker.image(IMAGE).inside {
                        sh '''
                            apt-get update
                            apt-get install -y openjdk-17-jdk build-essential
                            gem install --no-document fpm
                        '''
                    }
                }
            }
        }

        stage('Compile Java') {
            steps {
                script {
                    docker.image(IMAGE).inside {
                        sh '''
                            rm -rf build
                            mkdir build
                            javac -d build src/Hello.java
                            jar cfe hello.jar Hello -C build .
                        '''
                    }
                }
            }
        }

        stage('Prepare Package Directory') {
            steps {
                script {
                    docker.image(IMAGE).inside {
                        sh '''
                            mkdir -p package/usr/local/bin
                            cp hello.jar package/usr/local/bin/
                        '''
                    }
                }
            }
        }

        stage('Build DEB using FPM') {
            steps {
                script {
                    docker.image(IMAGE).inside {
                        sh '''
                            fpm -s dir -t deb -n hello-java -v 1.0.${BUILD_NUMBER} --prefix=/ -C package
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: '*.deb'
        }
    }
}