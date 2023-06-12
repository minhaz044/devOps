## Gitlab Installization  
  
  * The first thing you need to run is the update or upgrade.  
 ```sudo apt update```
 
 * Then install the necessary dependencies
 ``` sudo apt-get install -y curl openssh-server ca-certificates ```

* Installing GitLab CE
 
``` curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash ``` 
 * install GitLab CE package by using this command
 ``` sudo apt-get install gitlab-ce ``` 
 
 * If you want to set up using your server address, do the below    
  ``` sudo EXTERNAL_URL="http://gitlabce.example.com" apt-get install gitlab-ce ```

  * Once you have the package installed, you can run the provided configuration
utility. It provides an automatic configuration. You can modify things later if you
need to.

``` sudo gitlab-ctl reconfigure ```

``` gitlab-ctl start ```

 
 Access the Gitlab 
 
## Install Jenkins

* First, add the repository key to your system:

``` wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key |sudo gpg --dearmor -o /usr/share/keyrings/jenkins.gpg ```

* Next, let’s append the Debian package repository address to the server’s sources.list: 

 ``` sudo sh -c 'echo deb [signed-by=/usr/share/keyrings/jenkins.gpg] http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list' ```
 * update   
 ``` sudo apt update```
* Finally, install Jenkins and its dependencies:
  
 ``` install Jenkins and its dependencies: ```
 
* start the Jenkins   

``` sudo systemctl start jenkins.service ```
* Check Jenkins   

``` sudo systemctl status jenkins ```

## Install sonarqube

* I have followed this instraction for SonarInstilazition   
* I have faced Elastic search root suer related one issue , but i have forget how i solved the issue, i will add if i face in future

https://www.vultr.com/docs/how-to-use-sonarqube-on-ubuntu-22-04-lts/

* This is My Jenkins Script, I am trying to create a general script, so I can use it for all project  
https://github.com/minhaz044/devOps/blob/main/Jenkinsfile


### Steps in Jenkins

#### Dashboard => Manage Jenkins => Credentials

Add Credentials 

	GitLabUser 			=> UserName password
	SonarQubeSecretKey		=> Secret text
	Gitlab_api_token		=> Secret text
![Add Credentials](https://github.com/minhaz044/devOps/blob/main/key%20List.png)
#### Dashboard => Manage Jenkins => Configure System

Add  SonarQube servers   
![SonarQube servers](https://github.com/minhaz044/devOps/blob/main/sonarQubeServer.png)

#### Dashboard => Manage Jenkins => Global Tool Configuration  
JDK
![JDK](https://github.com/minhaz044/devOps/blob/main/JDK.png)
GIT
![GIT](https://github.com/minhaz044/devOps/blob/main/GIT.png)
 SonarQube Scanner 
![SonarQube Scanner](https://github.com/minhaz044/devOps/blob/main/sonarQubeScanner.png)
Maven
![Maven](https://github.com/minhaz044/devOps/blob/main/maven.png)

#### Dashboard => Manage Jenkins => Plugin Manager  
1. Folder Properties Plugin  
2. Git Parameter Plug-In

#### Create The pipeline 
1. Create a Folder with Team Name or Profile Name
2. In Folder property Section ,add GIT REPO URL ,I am following this pattern ,JOB_NAME+'REPOSITORY_URL'
![Folder property Section](https://github.com/minhaz044/devOps/blob/main/folderProperty.png)
3. Create new Item 
4. Add a pipeline script with project name just as git repo name ,![image](https://github.com/minhaz044/devOps/assets/30874666/5d48e71a-4faf-4abe-809f-20dbc4c5c36d)
5. Create the script Like below Screen shoot.![image](https://github.com/minhaz044/devOps/assets/30874666/d4cdc7b1-04b2-4b89-8200-2422dcf3099b)![image](https://github.com/minhaz044/devOps/assets/30874666/105cc670-098b-4f2c-a98e-dd36c194f256)![image](https://github.com/minhaz044/devOps/assets/30874666/e771cb07-a00a-4afd-bfb7-f6236b47d46a)![image](https://github.com/minhaz044/devOps/assets/30874666/ee38d395-c2df-4737-b352-3c61bbea7401)![image](https://github.com/minhaz044/devOps/assets/30874666/25810e4f-2ef5-4bf3-9da4-1dbba9dbecee)![image](https://github.com/minhaz044/devOps/assets/30874666/7d828649-236e-4beb-9e9c-b2ac721726bb)
6. Apply and Save
7. Build the Project,initial build might failed. ![image](https://github.com/minhaz044/devOps/assets/30874666/7309c350-b3ba-40a8-961c-197ad217af79)
8. After initial build new Field will be generated. ![image](https://github.com/minhaz044/devOps/assets/30874666/31fcb4e8-2eb9-46f9-b8b1-a9401481abdb) ![image](https://github.com/minhaz044/devOps/assets/30874666/e135c6ee-809d-4fc0-9ae8-ff42695293b0)
 

### Steps in SonarQube
1. Go to your profile , create token for Jenkins.![image](https://github.com/minhaz044/devOps/assets/30874666/1a192379-4c99-49a1-aa36-e0a947b32b49)
2. 







