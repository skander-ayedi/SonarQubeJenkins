pipeline {
    agent any
      environment {
          DOCKERHUB_CREDENTIALS=credentials('dockerhub')
        }
      stages {
          stage('Clone') {
            steps {
                   git branch: 'main', credentialsId: 'github', url:'https://github.com/skander-ayedi/test.git'
                  }
             }
          stage('composer') {
            steps {
                   sh 'sudo composer install'
                  }
             }
          stage('phpUnit') {
            steps {
                    sh './vendor/bin/phpunit'
                }
              }
          stage('phpCs') {
            steps {
                    sh 'sudo ./vendor/bin/phpcs ./app'
                  }
              }
          stage('Larastan') {
            steps {
                    sh 'sudo ./vendor/bin/phpstan analyse'
                }
              }
           stage("sonarqube") {
                steps { withSonarQubeEnv(installationName: 'sonar 8.3',credentialsId: 'sonarqube-token') {
                      sh """/var/lib/jenkins/workspace/mypipe/
                      sonar-scanner-4.7.0.2747
                      -linux/bin/sonar-scanner \
                      -Dsonar.projectKey=laravel \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=http://192.168.50.128:9000/ """
                      }
                    }
                  }

            stage('Docker Build') {
                  steps {
                          sh 'docker build -t skander2/test:latest .'
                      }
                     }
            stage('Login') {
                    steps {
                            sh 'echo $ DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    }
                  }
            stage('Push') {
                    steps {
                          sh 'docker push skander2/test:latest'
                        }
                  }
           }
}
