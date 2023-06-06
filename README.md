## Gitlab Installization  
  
  * The first thing you need to run is the update or upgrade.  
 ```sudo apt update```
 
 * Then install the necessary dependencies
 ` sudo apt-get install -y curl openssh-server ca-certificates `

* Installing GitLab CE
 
` curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash `  
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
 
