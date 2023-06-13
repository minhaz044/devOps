//currentBuild.displayName = "${env.gitlabSourceBranch}" // this will show branch name in side bar 

pipeline{
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '30'))
    }

    parameters {
        gitParameter branch: '', branchFilter: 'origin/(.*)', defaultValue: 'main', description: 'Select Branch Name', name: 'BRANCH',
        quickFilterEnabled: true, selectedValue: 'NONE', sortMode: 'NONE', tagFilter: '*', type: 'GitParameterDefinition', useRepository: '.*(?i)'+"${JOB_BASE_NAME}"
        booleanParam 'UPLOAD_TO_REMOTE'
    }
    
    environment {
        SOURCE_GIT_URL= getRepoUrl("${JOB_BASE_NAME}"+"REPOSITORY_URL")
        CHECKOUT_BRANCH = "${env.gitlabSourceBranch ?: BRANCH}"
        GITLAB_TOKEN = getGitLabApiSecret('Gitlab_api_token')
        PROJECT_ID = getProjectIdByRepoNameAndUrl("${SOURCE_GIT_URL}","${JOB_BASE_NAME}","${GITLAB_TOKEN}")
       
        
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
                updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'running',"${GITLAB_TOKEN}")
            }
            
        }        
        stage('Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('SonarQube analysis') {
            steps{
                withSonarQubeEnv('SonarQube') {
                sh 'mvn sonar:sonar -Dsonar.projectKey="${JOB_BASE_NAME}"'
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
        updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'failed',"${GITLAB_TOKEN}")
      }
      success {
        updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'success',"${GITLAB_TOKEN}")
      }
      aborted {
        updateCommitStatus("${PROJECT_ID}",getCurrentSHAkey(),'canceled',"${GITLAB_TOKEN}")
      }
    }


}
/*---------------------- Dependency Import -------------------*/

import groovy.json.JsonSlurper
import org.jenkinsci.plugins.credentialsbinding.Binding
import org.jenkinsci.plugins.credentialsbinding.impl.StringBinding
import org.jenkinsci.plugins.credentialsbinding.impl.UsernamePasswordBinding

/*------------------------------------------------------------*/

def getCurrentSHAkey(){
    return sh(script: 'git rev-parse HEAD', returnStdout: true).trim();
}

def getProjectIdByRepoNameAndUrl(String repoHttpUrl,String repoName,String gitlabToken){
    def repoId=-1;
        timeout(time: 20, unit: 'SECONDS') { 
            def numberOfTry = 5
            while(numberOfTry>0) {
                def response = httpRequest customHeaders: [[maskValue: true, name: 'PRIVATE-TOKEN', value: gitlabToken]], 
                 validResponseCodes: '199:499',url: 'http://192.168.96.6:9595/api/v4/search?scope=projects&search='+ repoName
                if(response.status < 399) {
                   def responseJson = new JsonSlurper().parseText(response.content)
                   for(repo in responseJson){
                        if(repoHttpUrl.trim().equalsIgnoreCase(repo.http_url_to_repo.trim())){
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

def updateCommitStatus(String repoId,String commitId,String state,String gitlabToken) {
    int numberOfTry=5
    timeout(time: 20, unit: 'SECONDS') { 
        while(numberOfTry>0) {
        def response = httpRequest httpMode: 'POST', customHeaders: [[maskValue: true, name: 'PRIVATE-TOKEN', value: gitlabToken]],
            validResponseCodes: '199:499', url: 'http://192.168.96.6:9595/api/v4/projects/' + repoId + '/statuses/' + commitId + '?' + 'state='+ state
        if(response.status  < 300) {
            break;
        }
        numberOfTry--
        }
    }
}

def getRepoUrl(String propertyKey){
    String url="";
    withFolderProperties{
        url= env."${propertyKey}";
    }
    return url;
}

def getGitLabApiSecret(String credentialsId) {
    withCredentials([string(credentialsId: credentialsId, variable: 'GITLAB_API_SECRET')]) {
        return env.GITLAB_API_SECRET
    }
}
