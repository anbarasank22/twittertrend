pipeline{
    agent {
        node {
            label "valaxy"
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.8.7/bin:$PATH"
    }
    stages {
        stage('build') {
            steps{
                echo "------------ build started ---------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "------------ build completed ---------"
        }
      }

        stage('Unit Test') {
            steps {
                echo '<--------------- Unit Testing started  --------------->'
                sh 'mvn surefire-report:report'
                echo '<------------- Unit Testing stopped  --------------->'
            }
        }

       stage ("Sonar Analysis") {
            environment {
               scannerHome = tool 'sonarsqube_scanner'
            }
            steps {
                echo '<--------------- Sonar Analysis started  --------------->'
                withSonarQubeEnv('sonarqube-cloud') {    
                    sh "${scannerHome}/bin/sonar-scanner"
                echo '<--------------- Sonar Analysis stopped  --------------->'
                }    
               
            }   
        }
        stage("Quality Gate") {
            
            steps {
                script {
                  echo '<--------------- Sonar Gate Analysis Started --------------->'
                    timeout(time: 1, unit: 'HOURS'){
                      def qg = waitForQualityGate()
                        if(qg.status !='OK') {
                            error "Pipeline failed due to quality gate failures: ${qg.status}"
                        }
                    }  
                  echo '<--------------- Sonar Gate analysis stoped ------------->'

                }
            }
        }

     }

}