node{
  def Namespace = "default"
  def ImageName = "sayarapp/sayarapp"
  def Creds	= "2dfd9d0d-a300-49ee-aaaf-0a3efcaa5279"
  try{
  stage('Checkout'){
      git 'https://github.com/vivekaiyar/ci-cd-k8s.git'
      sh "git rev-parse --short HEAD > .git/commit-id"
      imageTag= readFile('.git/commit-id').trim()



  }


  stage('RUN Unit Tests'){
      sh "npm install"
      sh "npm test"
  }
  stage('Docker Build, Push'){
    withCredentials([usernameColonPassword(credentialsId: 'art4lab0-docker-deploy', variable: 'docker_deploy')]) {
            sh "docker build -t ${ImageName}:${imageTag} ."
            sh "docker tag ${ImageName}:${imageTag} art4lab0.labs.mastercard.com:5001/artifactory/docker-internal/test/${ImageName}:${imageTag}"
            sh "docker push art4lab0.labs.mastercard.com:5001/artifactory/docker-internal/test/${ImageName}:${imageTag}"
        }
       
    }
    stage('Deploy on K8s'){

     sh "ansible-playbook /var/lib/jenkins/ansible/sayarapp-deploy/deploy.yml  --user=jenkins --extra-vars ImageName=${ImageName} --extra-vars imageTag=${imageTag} --extra-vars Namespace=${Namespace}"
    }
     } catch (err) {
      currentBuild.result = 'FAILURE'
    }
}
