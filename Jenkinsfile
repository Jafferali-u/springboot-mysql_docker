currentBuild.displayName = "springboot-#"+currentBuild.number
pipeline{ 
  agent any   
    environment{
        PATH = "/opt/maven3/bin:$PATH"
    }
  stages{
    
    stages{
      stage("code check"){
            steps{
                withSonarQubeEnv('sonar') {
                    sh "mvn -Dmaven.test.skip=true sonar:sonar"
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
