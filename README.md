# deploying-to-EKS-cluster-from-jenkins-node
deploying-to-EKS-cluster-from-jenkins-node

* 1- eks cluster is up and running
* 2- jenkins is up and running
* 3- Install jenkins plugins:
  * Docker, 
  * Docker pipeline,
  * Kubernetes Continuous Deploy


## Jenkins-master-setup

```
#Change Host Name to Jenkins
sudo hostnamectl set-hostname Jenkins

#Install JDK on AWS EC2 Instance
sudo apt-get update 
sudo apt install openjdk-11-jre-headless -y
java -version

#Install and Setup Jenkins
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null  

sudo apt-get update
sudo apt-get install jenkins -y

#install maven
sudo apt-get update
sudo apt install maven -y

#getting the initial psw
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

#### Jenkins commands

```
sudo service jenkins status

# enable the Jenkins service to start at boot with the command
#sudo systemctl enable jenkins

#start the Jenkins service with the command:

#sudo systemctl start jenkins
```

### Jenkins Global Tool Configuration

### Gradle 

Manage Jenkins -> Global Tool Configuration -> Gradle
Setup Gradle


###  configure Jenkins Jenkins user to run Docker builds

Add jenkins user to sudoers.

```
sudo cat >>/etc/sudoers <<EOF
jenkins ALL=(ALL) NOPASSWD: ALL 
EOF
sudo su - jenkins 
```


```
sudo apt update
sudo apt-get install maven -y
```

### Install docker

```
sudo apt-get update && sudo apt install docker.io -y
#Add jenkins user to Docker group
sudo usermod -aG docker jenkins
#Restart Docker service
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl restart docker
```

##2- EKS Setup

### install aws-cli

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt update
sudo apt install unzip -y
sudo unzip awscliv2.zip
sudo ./aws/install
aws --version
```

### install Eksctl and kubectl

```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

eksctl version
kubectl version --short --client
```


aws-configure


## install aws iam authenticator
curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.5.9/aws-iam-authenticator_0.5.9_linux_amd64
chmod +x ./aws-iam-authenticator
sudo mv ./aws-iam-authenticator /usr/local/bin/

