node{
  def Namespace = "default"
  def ImageName = "vivekaiyar/node"
  def Creds	= "39df95e1-f34f-44c8-8cdc-98989764513b"
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
    script {
    withDockerRegistry(credentialsId: '39df95e1-f34f-44c8-8cdc-98989764513b', url: ''https://index.docker.io/v1/'') {
            sh "docker build -t ${ImageName}:${imageTag} ."
            //sh "docker tag ${ImageName}:${imageTag} art4lab0.labs.mastercard.com:5001/artifactory/docker-internal/test/${ImageName}:${imageTag}"
            sh "docker push ${ImageName}:${imageTag}"
    }
        }
       
    }
    stage('Deploy on K8s'){

     sh "ansible-playbook /var/lib/jenkins/ansible/sayarapp-deploy/deploy.yml  --user=jenkins --extra-vars ImageName=${ImageName} --extra-vars imageTag=${imageTag} --extra-vars Namespace=${Namespace}"
    }
     } catch (err) {
      currentBuild.result = 'FAILURE'
    }
}
