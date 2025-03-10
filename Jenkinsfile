pipeline {
    agent any
    environment{
        NETLIFY_SITE_ID = '8068b3d5-de1d-4df6-8743-34cd20345776'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            #test -f build/index.html
                            npm test
                        '''
                    }
                    post {
                        always {
                            sh 'ls -R test-results'
                            junit 'test-results/junit.xml'
                        }
                    }
                }
            } // <-- Closing the 'parallel' block properly
        } // <-- Closing the 'Tests' stage properly

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  npm install netlify-cli 
                  node_modules/.bin/netlify --version
                  echo 'Deploying to Production on site id : $NETLIFY_SITE_ID'
                  node_modules/.bin/netlify status
                  node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}
