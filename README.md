# deploying-to-EKS-cluster-from-jenkins-node

<p align="center">
  <img src="https://user-images.githubusercontent.com/96833570/222667672-caa236ad-4349-4308-9d0f-6d7805077b3e.png" />
</p>




* 1- eks cluster is up and running
* 2- jenkins is up and running


## Jenkins-setup

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

## 2- EKS Setup

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


Run `aws-configure` and configure defaults.


## install aws iam authenticator

```
curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.5.9/aws-iam-authenticator_0.5.9_linux_amd64
chmod +x ./aws-iam-authenticator
sudo mv ./aws-iam-authenticator /usr/local/bin/
```
### Creating the eks cluster

You can this [java maven repo](https://github.com/colossus06/java-maven).

`eksctl create cluster --name clu --region us-east-1 --nodegroup-name my-nodes --node-type t3.small --managed --nodes 1 `

![image](https://user-images.githubusercontent.com/96833570/222448292-d29254db-6029-4dd8-8d88-30fcf68efdb0.png)


`kubectl get node`

![image](https://user-images.githubusercontent.com/96833570/222452891-8a8a90ff-fdd1-4da5-a340-188608d13a89.png)


![image](https://user-images.githubusercontent.com/96833570/222506434-3dd3a1dc-560f-46c7-ba47-586e77b4c53f.png)




`eksctl delete cluster --name clu`
