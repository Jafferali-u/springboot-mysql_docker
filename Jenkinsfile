currentBuild.displayName = "springboot-#"+currentBuild.number
pipeline{ 
  agent any   
    environment{
        PATH = "/opt/maven3/bin:$PATH"
    }
  stages{
    stage("code check"){
            steps{
                withSonarQubeEnv('sonar') {
                    sh "mvn -Dmaven.test.skip=true sonar:sonar"
                }
            }
        }
    stage('Sonar scan result check') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    retry(3) {
                        script {
                            def qg = waitForQualityGate()
                            if (qg.status != 'OK') {
                                error "Pipeline aborted due to quality gate failure: ${qg.status}"
                            }
                            if (qg.status == 'OK') {
                                echo "Pipeline quality gate Pass!!: ${qg.status}"
                            }
                        }
                    }
                }
            }
        }
          stage("mevan build"){
            steps {
                sh "mvn package -Dmaven.test.skip=true"
            }
        }
  }
}
