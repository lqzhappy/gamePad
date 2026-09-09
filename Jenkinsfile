pipeline {

    agent none

    stages {

        stage('Build Web') {

            agent {
                docker {
                    image 'node:22'
                    label 'docker-agent-auto'
                    args '-v /ext/jenkins-agent/npm-cache:/root/.npm'
                }
            }

            steps {

                echo '===== Node 环境 ====='

                sh '''
                    node -v
                    npm -v
                '''


                echo '===== 安装依赖 ====='

                sh '''
                    npm ci
                '''


                echo '===== Webpack Build ====='

                sh '''
                    npm run build
                '''

            }

        }


        stage('Check Build') {

            agent {
                label 'docker-agent'
            }

            steps {

                sh '''
                    echo "===== Build Result ====="

                    ls -lah

                    ls -lah dist
                '''

            }

        }

    }

}
