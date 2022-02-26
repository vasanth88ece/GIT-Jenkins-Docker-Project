node{
     
    def buildnumber = BUILD_NUMBER 
    stage('GIT Checkout'){
        git url: 'https://github.com/vasanth88ece/java-web-app-docker.git',branch: 'master'
    }
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven", type: "maven"
       sh "${mavenHome}/bin/mvn clean package"
      
    } 

    stage("Build Docker Image"){
        sh "docker build -t vasanth88ece/javawebapp:${buildnumber} ."
    }
    stage("Docker Login and Push"){
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
            sh "docker login -u vasanth88ece -p ${Docker_Hub_Pwd}"
        }
        sh "docker push vasanth88ece/javawebapp:${buildnumber}"
    }
    stage("Deployment as container on deployment server"){
        
       def dockerRun = 'docker run -d -p 8080:8080 --name javawebappcont vasanth88ece/javawebapp:${buildnumber}'
         
         sshagent(['Dep_Server_SSH']) {
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.47.142 docker rm -f javawebappcont || true"
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.47.142 docker run -itd --name javawebappcont -p 8010:8080 vasanth88ece/javawebapp:${buildnumber}" 
        }
    }
}
