CLO835 Summer 2024 Assignment 1

This repository contains the instructions and code for setting up a simple multi-container Docker application with Terraform and AWS. Follow the steps below to clone the repository, write the Terraform code, configure your environment, and run the Docker containers.

Prerequisites
Git
Terraform
AWS CLI
Docker

Clone the Repository
First, clone the repository to your local machine.
git clone https://github.com/ladunuthala/clo835_summer2023_assignment1.git
Write Terraform Code
Add your Terraform code to the cloned repository.

Git Workflow
Check the status, add changes, commit, and push to a new remote repository.
git status
git add -A
git commit -m "Message"
git remote set-url origin https://github.com/nishitrathod412/clo835_summer2024_assignment_1.git
git push origin main

Terraform Setup
Navigate to the Terraform directory, generate SSH keys, and initialize Terraform.
cd terraform
ssh-keygen -t rsa -f clo835
terraform init
terraform validate
terraform plan
terraform apply

Connect to EC2 Instance
SSH into the EC2 instance using the generated key. Make sure to open port 22 in the security inbound rules and import the key pair into the EC2 instance.
ssh -i clo835 ec2-user@3.80.58.102

Docker Setup
Install and start Docker on the EC2 instance.
cd ~/.ssh
sudo yum install docker
sudo systemctl status docker
sudo systemctl start docker
sudo systemctl enable docker

Check Docker socket and add the user to the Docker group.
ls -ltra /var/run/docker.sock
sudo usermod -a -G docker ec2-user

AWS Credentials
Export your AWS credentials.
export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AWS_SESSION_TOKEN=

Docker Login to AWS ECR
Log in to the AWS ECR repository.
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789.dkr.ecr.us-east-1.amazonaws.com/assign1mysqldbrepo

Docker Network
Create a Docker network.
sudo docker network create net --subnet 182.18.0.0/24 --driver bridge

Run MySQL Container
Run the MySQL container.
docker run --network net -d -e MYSQL_ROOT_PASSWORD=pw 937274605158.dkr.ecr.us-east-1.amazonaws.com/assign1mysqldbrepo:v0.1

Inspect Container
Inspect the running container to get its details.
docker inspect <>

Environment Variables
Set the environment variables for the application containers.
export DBHOST=182.18.0.2
export DBPORT=3306
export DBUSER=root
export DATABASE=employees
export DBPWD=pw

Run Web Application Containers
Run the web application containers with different colors.
docker run --name blue --network net -d -p 8081:8080 -e APP_COLOR=blue -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e DBUSER=$DBUSER -e DBPWD=$DBPWD 123456789.dkr.ecr.us-east-1.amazonaws.com/assign1webapprepo:v0.1

docker run --name pink --network net -d -p 8082:8080 -e APP_COLOR=pink -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e DBUSER=$DBUSER -e DBPWD=$DBPWD 123456789.dkr.ecr.us-east-1.amazonaws.com/assign1webapprepo:v0.1

docker run --name lime --network net -d -p 8083:8080 -e APP_COLOR=lime -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e DBUSER=$DBUSER -e DBPWD=$DBPWD 123456789.dkr.ecr.us-east-1.amazonaws.com/assign1webapprepo:v0.1

MySQL Database Verification
Access the MySQL database inside the container and verify the contents.
docker exec -it c101b0bd3742 /bin/bash
mysql -uroot -ppw
show databases;
use employees;
show tables;
select * from employee;
Container Network Testing
Test the network connectivity between containers.

Blue (Port 8081 and IP 182.18.0.3)
docker exec -it blue sh
apt-get update -y
apt install iputils-ping
ping lime
ping pink

Pink (Port 8082 and IP 182.18.0.4)
docker exec -it pink sh
apt-get update -y
apt install iputils-ping
ping blue
ping lime

Lime (Port 8083 and IP 182.18.0.5)
docker exec -it lime sh
apt-get update -y
apt install iputils-ping
ping blue
ping pink
Application Verification

Verify the applications are running correctly by curling the endpoints.
curl localhost:8081
curl localhost:8082
curl localhost:8083

--------------------------------------------------------------------

# Install the required MySQL package

sudo apt-get update -y
sudo apt-get install mysql-client -y

# Running application locally
pip3 install -r requirements.txt
sudo python3 app.py
# Building and running 2 tier web application locally
### Building mysql docker image 
```docker build -t my_db -f Dockerfile_mysql . ```

### Building application docker image 
```docker build -t my_app -f Dockerfile . ```

### Running mysql
```docker run -d -e MYSQL_ROOT_PASSWORD=pw  my_db```


### Get the IP of the database and export it as DBHOST variable
```docker inspect <container_id>```


### Example when running DB runs as a docker container and app is running locally
```
export DBHOST=127.0.0.1
export DBPORT=3307
```
### Example when running DB runs as a docker container and app is running locally
```
export DBHOST=172.17.0.2
export DBPORT=3306
```
```
export DBUSER=root
export DATABASE=employees
export DBPWD=pw
export APP_COLOR=blue
```
### Run the application, make sure it is visible in the browser
```docker run -p 8080:8080  -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD  my_app```
