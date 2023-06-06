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
