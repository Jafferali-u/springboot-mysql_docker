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
    stage("file share"){
        steps{
            sshagent(['srcjenkins']) {
                sh """
                scp -o StrictHostKeyChecking=no DockerSpringBoot-0.0.1-SNAPSHOT.war srcjenkins@172.31.57.87:/opt/springboot-mysql_docker/
                ssh -o StrictHostKeyChecking=no srcjenkins@172.31.57.87 docker build . -t springboot-mysql_docker -f /opt/springboot-mysql_docker/Dockerfile
                """
                }
            }
        }
    }
}
