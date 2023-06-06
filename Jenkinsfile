//currentBuild.displayName = "${env.gitlabSourceBranch}" // this will show branch name in side bar 

pipeline{
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '30'))
    }

    parameters {
        gitParameter branch: '', branchFilter: 'origin/(.*)', defaultValue: 'main', description: 'Select Branch Name', name: 'BRANCH',
        quickFilterEnabled: true, selectedValue: 'NONE', sortMode: 'NONE', tagFilter: '*', type: 'GitParameterDefinition', useRepository: '.*'+"${JOB_BASE_NAME}"
        booleanParam 'UPLOAD_TO_REMOTE'
    }
    
    environment {
        SOURCE_GIT_URL= "${'http://192.168.96.6:9595/gitlab-instance-ee8a77a2/' + JOB_BASE_NAME +'.git' }"
        CHECKOUT_BRANCH = "${env.gitlabSourceBranch ?: BRANCH}"
        PROJECT_ID = getProjectIdByRepoNameAndUrl("${SOURCE_GIT_URL}","${JOB_BASE_NAME}")
    }

    tools {
        maven 'MVN3.6.3';
    }
    stages{
        stage('Checkout'){
            steps{
                //updateGitlabCommitStatus name: 'build', state: 'running'
                echo "........................Git Checkout Started.................."
                git branch: "${CHECKOUT_BRANCH}", credentialsId: 'GitLabUser', url: "${SOURCE_GIT_URL}"
                updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'running')
            }
            
        }        
        stage('Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('SonarQube analysis') {
        //def scannerHome = tool 'SonarScanner 4.0';
            steps{
                withSonarQubeEnv('SonarQube') {
                // If you have configured more than one global server connection, you can specify its name
                //sh "${scannerHome}/bin/sonar-scanner"
                //    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=RestApi'
                sh 'mvn sonar:sonar -Dsonar.projectKey=RestApi'
                }
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true , credentialsId: 'SonarQubeSecretKey'
                }
            }
        }
        

        stage('Upload To Remote') {
            when { expression { return env.UPLOAD_TO_REMOTE == 'true'} }
            steps {
               echo " File uploaded to remote server."
            }
        }
    }
    
    post {
      failure {
        updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'failed')
      }
      success {
        updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'success')
      }
      aborted {
        updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'canceled')
      }
    }


}
/*---------------------- Dependency Import -------------------*/

import groovy.json.JsonSlurper

/*------------------------------------------------------------*/

def getCurrentSHAkey(){
    return sh(script: 'git rev-parse HEAD', returnStdout: true).trim();
}

def getProjectIdByRepoNameAndUrl(String repoHttpUrl,String repoName){
    def repoId=-1;
        timeout(time: 20, unit: 'SECONDS') { 
            def numberOfTry = 5
            while(numberOfTry>0) {
                def response = httpRequest customHeaders: [[maskValue: true, name: 'PRIVATE-TOKEN', value: 'glpat-mXwkhiYf4tcYJVnvZm6C']], 
                 validResponseCodes: '199:499',url: 'http://192.168.96.6:9595/api/v4/search?scope=projects&search='+ repoName
                if(response.status == 200) {
                   def responseJson = new JsonSlurper().parseText(response.content)
                   for(repo in responseJson){
                       if(repoHttpUrl.equalsIgnoreCase(repo.http_url_to_repo.trim())){
                           repoId = repo.id
                       }
                   }
                break;
               }
               numberOfTry--
            }
        }
        return repoId;
}

def updateCommitStatus(String repoId,String commitId,String state) {
    int numberOfTry=5
    timeout(time: 20, unit: 'SECONDS') { 
        while(numberOfTry>0) {
        def response = httpRequest httpMode: 'POST', customHeaders: [[maskValue: true, name: 'PRIVATE-TOKEN', value: 'glpat-mXwkhiYf4tcYJVnvZm6C']],
            validResponseCodes: '199:499', url: 'http://192.168.96.6:9595/api/v4/projects/' + repoId + '/statuses/' + commitId + '?' + 'state='+ state
        if(response.status == 201) {
            break;
        }
        numberOfTry--
        }
    }
}
