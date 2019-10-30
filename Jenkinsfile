pipeline{
  agent {
    label('Demo')
  }
  tools {
    nodejs('Node12')
  }
  
  stages{
    stage('SonarScanner') {
            steps {
                    script {
                        sh """
                        sonar-scanner -Dsonar.projectKey=student16-project -Dsonar.sources=. -Dsonar.host.url=https://master.jenkins-practice.tk:8443 -Dsonar.login=8f65556dbeee967d0c5916c6eb4f96bd033ca80f
                        """
                    }
                }
            }

    stage('Build') { 
    when {
            branch 'master'
                }           
      parallel {
        stage ('build css') {
          steps{
            script{
              sh """
                cd ${WORKSPACE}/www/css
                cleancss -d style.css > ../min/custom-min.css """ 
            }
          }    
        }
        stage ('build js') {
          steps {
            script{
              sh """
                cd ${WORKSPACE}/www/js
                uglifyjs --timings init.js -o ../min/custom-min.js """
            }
          }    
        }        
      }
    }
    stage ('Upload artifact'){
           when {
            branch 'master'
                }         
      steps {
        script { 
          sh """
            cd ${WORKSPACE}/www
            tar --exclude='./css' --exclude='./js' -c -z -f ../.tgz ."""
          nexusArtifactUploader artifacts: [[artifactId: 'artifact-student16', \
                                            classifier: '', file: 'artifact-student16.tgz', \
                                            type: 'tgz']], \
                                            credentialsId: 'student16-jenkins', \
                                            groupId: '', \
                                            nexusUrl: 'master.jenkins-practice.tk:9443', \
                                            nexusVersion: 'nexus3', \
                                            protocol: 'https', \
                                            repository: 'student16-raw-hosted-repo', \
                                            version: '${BASE_VERSION}-${BUILD_NUMBER}' 
        }
      }
    }        
  }
}