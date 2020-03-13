pipeline{
 agent { label 'myagent'}
 stages{
  stage('Initializing'){
     steps{
     sh """
         docker rmi -f \$(docker images -q) || date
   """
   }
  }
  stage('Update Maven Artifacts'){
   steps{
   sh """
   cp /root/python-scripts/update-data-service-index-url .
   cp /root/python-scripts/update_jobs_service_url .
   python update_jobs_service_url
   python update-data-service-index-url
   """
   }
  }
   stage('Build'){
       steps{
           withDockerRegistry([ credentialsId: "tarkhand-rregistry", url: "https://registry.redhat.io" ]){
               sh """ 
               export MAVEN_MIRROR_URL=http://nexus3-kogito-tools.apps.kogito.automation.rhmw.io/repository/maven-public/
               cd s2i && make build
               """
           }
       }
   }
   stage('Test'){
       steps{
           sh """
           export MAVEN_MIRROR_URL=http://nexus3-kogito-tools.apps.kogito.automation.rhmw.io/repository/maven-public/
           cd s2i && make test
           """
       }
   }
   stage('Finishing'){
       steps{
           sh"""
           docker rmi -f \$(docker images -q) || date
           """
       }
   }
 }
}
