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
                
                scp -o StrictHostKeyChecking=no target/docker-springboot.war srcjenkins@172.31.57.87:/opt/springboot-mysql_docker/
                
                """
                }
            }
        }
    stage("deploy"){
            steps{
                sshagent(['ansadmin_ansible']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ansadmin@172.31.63.160 ansible-playbook springboot-mysql_docker-push.yaml
                    ssh -o StrictHostKeyChecking=no ansadmin@172.31.63.160 ansible-playbook springboot-mysql_kubernetes.yaml
                    """
                }
            }
        }
    }
}
