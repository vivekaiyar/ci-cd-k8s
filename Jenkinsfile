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
    withDockerRegistry([credentialsId: "${Creds}", url: 'art4lab0.labs.mastercard.com:6565']) {
      sh "docker build -t ${ImageName}:${imageTag} ."
      sh "docker push ${ImageName}"
        }
    //sh "docker build -t ${ImageName}:${imageTag} ."
    //sh "docker push ${ImageName}"

    }
    stage('Deploy on K8s'){

     sh "ansible-playbook /var/lib/jenkins/ansible/sayarapp-deploy/deploy.yml  --user=jenkins --extra-vars ImageName=${ImageName} --extra-vars imageTag=${imageTag} --extra-vars Namespace=${Namespace}"
    }
     } catch (err) {
      currentBuild.result = 'FAILURE'
    }
}
