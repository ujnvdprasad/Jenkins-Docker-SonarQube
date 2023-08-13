# Here are the step-by-step details to set up an end-to-end Jenkins pipeline for a Java application using Jenkins,SonarQube,Docker and minikube:

# Prerequisites:

## Java application code hosted on a Git repository

Jenkins server

SonarQube

Docker

# Install Jenkins, configure Docker as agent, set up cicd and much more.

# AWS EC2 Instance

Go to AWS Console

Instances(running)

Launch instances
![Screenshot 2023-08-13 123545](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/3e77834e-f38e-4515-99a2-392e4d10bd40)


## Install Jenkins.

Pre-Requisites:

Java (JDK)

# Run the below commands to install Java and Jenkins

Install Java

```
sudo apt update
sudo apt install openjdk-11-jre
```

Verify Java is Installed

```
java -version
```

Now, you can proceed with installing Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

EC2 > Instances > Click on

In the bottom tabs -> Click on Security

Security groups

Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed All traffic).
![Screenshot 2023-08-13 124007](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/f4a7c261-df38-4e58-86e5-d9285116be03)


# Login to Jenkins using the below URL:
http://:8080 [You can get the ec2-instance-public-ip-address from your AWS EC2 console page]

Note: If you are not interested in allowing All Traffic to your EC2 instance 1. Delete the inbound traffic rule for your instance 2. Edit the inbound traffic rule to only allow custom TCP port 8080

After you login to Jenkins, - Run the command to copy the Jenkins Admin Password - sudo cat /var/lib/jenkins/secrets/initialAdminPassword - Enter the Administrator password
![Screenshot 2023-08-13 124112](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/9bebbeef-bb97-4640-b321-2fc7f9cac4ff)

## Click on Install suggested plugins
![Screenshot 2023-08-13 130442](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/7f7f0737-72cf-442b-a87d-35e09ef83894)

## Wait for the Jenkins to Install suggested plugins
![Screenshot 2023-08-13 130524](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/089d44be-0762-4251-b3cc-e1e1baec495b)

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]
![Screenshot 2023-08-13 124457](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/3dbd2d5c-7173-4082-a5e0-c7dd1e0a30c1)

Jenkins Installation is Successful. You can now starting using the Jenkins

# Install the Docker Pipeline plugin & SonarQube Scanner in Jenkins:

Log in to Jenkins.

Go to Manage Jenkins > Manage Plugins.

In the Available tab, search for "Docker Pipeline".

Select the plugin and click the Install button.

In the Available tab, search for "SonarQube Scanner".

Select the plugin and click the Install button.

Restart Jenkins after the plugin is installed.
![Screenshot 2023-08-13 134615](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/683a6cb4-81a3-4f23-8750-77b87a527f58)

![Screenshot 2023-08-13 124913](https://github.com/ujnvdprasad/Jenkins-Docker-SonarQube/assets/124246569/7e14749e-792a-49f4-b93f-acf14aaf05ea)

Wait for the Jenkins to be restarted.

## Docker Slave Configuration

Run the below command to Install Docker

```
sudo apt update
sudo apt install docker.io
```

## Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Once you are done with the above steps, it is better to restart Jenkins.

http://<ec2-instance-public-ip>:8080/restart

The docker agent configuration is now successful.

## Configure a Sonar Server locally

```
apt install unzip -y
adduser sonarqube
sudo su - sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip *
sudo -i
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
exit
sudo su - sonarqube
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start
```

Hurray !! Now you can access the SonarQube Server on http://<ip-address>:9000
