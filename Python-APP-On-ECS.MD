## Deploying a Python Flask Application with Amazon ECS


Introduction

Flask is an unopinionated Python micro-framework that makes it easy to develop web applications. In this hands-on lab, we will deploy a Python Flask application to the AWS Elastic Container Service (ECS). We will complete the following tasks:

- Download and configure a Flask application
- Create a Docker container image for the application
- Push the image to ECR
- Use ECS and Fargate to deploy the containerized application
## Create a ec2 instance and login to it 

## Create an Image and Push It to Amazon ECR
Create the Image
In your terminal window, update the EC2 instance.
```sh
sudo yum update -y
```
Enter your password at the prompt.
Install Docker.
```sh
sudo yum install -y docker
```
Start Docker.
```sh
sudo service docker start
```
Modify the users and groups on the EC2 instance.
```sh
sudo usermod -a -G docker $USER
```
Verify that the previous command was successfully applied.
```sh
docker info
```
Note: If this command fails, log out of the instance and then log back in.

Download the Flask application from GitHub.
```sh
git clone https://github.com/sforcloud/aws-ecs-app.git
```
List the contents of the current directory to view the application.
ls

List the available images.
```sh
docker image ls
```
Create a new image.
```sh
docker build -t cda-flask-app .
```
List the contents of the Dockerfile.
```sh
cat Dockerfile
```
Verify that the image was successfully built.
```sh
docker image ls
```
List the contents of the app.py file.
```sh
cat app.py
```
Try connecting to the application.
```sh
curl localhost
```
This command will fail because we need to reference the localhost of our EC2 instance.

Run the following command to run the image on our EC2 instance:
```sh
docker run -p 80:80 cda-flask-app
```
## Push the Image to Amazon ECR
- Create a new repository.
```sh
aws ecr create-repository --repository-name cda-penguin-app
```
Copy the value forrepositoryUri from the output, and save it somewhere convenient — we'll need it later.

Log in to the ECR repository.
```sh
aws ecr get-login --region us-east-1 --no-include-email
```
Copy the output of this command, then paste it into the command line and run it.
List the currently available images.
```sh
docker image ls
```
Associate the cda-flask-app image with the repository URI we copied earlier.
```sh
docker tag cda-flask-app:latest <REPOSITORY_URI>;
```
Push the image to Amazon ECR.
```sh
docker push <REPOSITORY_URI>;
```

- Switch to your AWS Management Console window, and navigate to the Elastic Container Service.
- Click Repositories in the left sidebar.
- The cda-penguin-app repository should appear in the list.
- Close your terminal window.

## Deploy the Containerized Application to ECS
1. On the Repositories page of the AWS Management Console, click Clusters in the left sidebar.
2. Click Get Started.
3. Under the Container definition header, click Configure in the custom box.
4. In the Edit container menu, enter the following settings:
    - Container name: penguin-container
    - Image: ;
    - Memory Limits (MiB): Hard limit | 128
    - Port mappings: 80

5. Click Update.

6.Under the Task definition header, click Edit, and enter the following settings:
   -  Task definition name: penguin-task-definition
   -  Task execution role: ecsTaskExecutionRole
   
        Note: Be sure to click into the Task execution role dropdown and select the ecsTaskExecutionRole option, even if it already appears by default. Otherwise, you may get an error.

7. Click Save.
8. Click Next.
9. Under the Define your service header, leave the default None option selected for Load balancer type.
10. Click Next.
11. Under the Configure your cluster header, type "penguin-default-cluster" for the 12. Cluster name.
12. Click Next.
13. Click Create.
14. When your resources are finished creating, click View service.
15. Click the Tasks tab at the bottom of the page.
16. Click the task name link to open it.
17. When the status of the container is RUNNING, open the ENI Id link under the Network header in a new browser tab.
18. Copy the IPv4 Public IP address for the application, and paste it in a new browser tab.
19. You should see our penguin website.
