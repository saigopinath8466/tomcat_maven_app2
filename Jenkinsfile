pipeline {
    agent none
    environment {
        DOCKER_LOGIN = credentials('docker_login')
    }

    stages {
        stage('checkout and build') {
            agent {
                docker{
                    image '846606/docker1:test2'
                    label 'dockernode'
                }
            }
          steps {
                sh "mvn clean package"
            }
        }
        stage ('create image') {
            agent {
                label 'dockernode'
            }
        
           steps {
              sh '''
              docker build -t 846606/docker1:tomcat-$BUILD_NUMBER .
              docker login -u $DOCKER_LOGIN_USR -p $DOCKER_LOGIN_PSW
              docker push 846606/docker1:tomcat-$BUILD_NUMBER
               docker rmi 846606/docker1:tomcat-$BUILD_NUMBER
        
             '''
             }

        }
        stage ('deploy') {
            agent {
                label 'dockernode'
            }

            steps {
                sh '''
                process_check=$(docker ps | grep -i "docker1:tomcat" | wc -l)
                if ["$process_check" -eq "1"]; then
                docker kill $(docker ps | grep -i "docker1:tomcat" | awk '{ print $1 }')
                fi
                docker run -d -p 8080:8080 846606/docker1:tomcat-$BUILD_NUMBER
                '''
            }
        }
    }
}
