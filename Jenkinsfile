currentBuild.displayName = "springboot-#"+currentBuild.number
pipeline{ 
  agent any   
    environment{
        PATH = "/opt/maven3/bin:$PATH"
    }
  stages{
          stage("mevan build"){
            steps {
                sh "mvn package -Dmaven.test.skip=true"
            }
        }
  }
}
