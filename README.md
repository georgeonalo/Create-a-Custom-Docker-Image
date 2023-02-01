# Create a Custom Docker Image and Push to ECR


In this project I will demonstrate how to quickly create a custom Docker image. I will create my own image using Nginx and add a file to it that states the date and time the container was deployed. I will then save the container data to AWS Elastic Container Registry (ECR).

###### Prerequisites
* You will need an account with Docker
* Docker desktop installed
* Visual Studio Code installed
* AWS account
* AWS CLI configured

#### Step 1

In Visual Studio Code you will need to open a terminal. You will also need to create a new directory. I called mine “Everything_docker” and have it connected to my Github account to easily commit files and code to. this directory is where we will put our Dockerfile as well as a index.html file. From your new directory you can click New File and name it Dockerfile.

![image](https://user-images.githubusercontent.com/115881685/216082829-772ac048-ce0f-4195-8738-796221714941.png)




Dockerfiles are used to generate a new image quickly and effectively, that contains the necessary content. Our Dockerfile will be very simple with a FROM step and COPY step. The FROM step will be the nginx image that will be downloaded from Docker hub. The COPY step will be the html file and the path where Nginx uses the index.html file from. In the Dockerfile on VS code put in the following.

![image](https://user-images.githubusercontent.com/115881685/216083784-939bd610-6104-4a88-8636-807d66dc9f4c.png)


Save and then create an index.html file in the same directory. Copy and paste the following Gist into your index.html file. You can edit the code or create your own!


![image](https://user-images.githubusercontent.com/115881685/216084814-8c813616-bec6-441f-b079-962c5e4fb7f5.png)
![image](https://user-images.githubusercontent.com/115881685/216084888-97314170-3a20-4f2b-909c-03ddacc2d506.png)



This html file will show the date and time that the container is created upon entering the website. This is how it looks in VS code. You can also select different colors.



![image](https://user-images.githubusercontent.com/115881685/216085062-1a005a1d-b57f-49bd-8992-9eea0a5a8bdf.png)



#### Step 2

Now to build this image! Ensure you are working in the correct directory, mine being “Everything_docker”. Input the following command into the terminal in VS code.

```
docker build -t docker-image .
```


This will build the image from the Dockerfile in the current directory and tag it, name it, “docker-image”. Once it has completed, type in docker image ls . This will list the docker images.


![image](https://user-images.githubusercontent.com/115881685/216086183-3b89171c-d196-4f0b-a8dd-5222c498ff9b.png)



#### Step 3


Now that our Docker image is made we can deploy our container! Run the following command in your terminal.


```
docker run -d --name project-conatiner -p 8080:80 docker-image
```

I will breakdown this command for you.

* Docker run deploys the container.
* -d detaches the container.
* — name here you can name your container.
* -p publish to a port, I am using 8080, exposes port 80 from the container on your docker host on port 8080.
* Lastly the image you are running, ours is the new docker image we created.


When you run the code you will get back a long string, this is the container ID. Just to have a look at our containers you can put in docker ps . This will show the running containers.


![image](https://user-images.githubusercontent.com/115881685/216087174-bd922826-03a6-4e95-b65d-771b973e42e1.png)



You can also look on Docker desktop to see your containers and images. Now to see the website we can type in a browser http://localhost:8080 or we can open it from Docker desktop as shown below.



![image](https://user-images.githubusercontent.com/115881685/216087559-3358ec55-0d19-4382-b886-c184111d06e6.png)


Let’s see if it worked…



![image](https://user-images.githubusercontent.com/115881685/216087747-cf0383f7-85c0-449b-ae89-6eff66ca9e42.png)



And it did!



#### Step 4

Now to save our container data to the AWS ECR. First we will create a repository in ECR. Input the following command in the VS code terminal. You may need to first configure with AWS credentials.


```
aws ecr create-repository --repository-name everything-docker --region us-east-1
```


Be sure to put in your own repo name and the correct region. The output will show the repo ARN as well as other information. To double check that it was made you can navigate to AWS > Amazon ECR > Repositories.





![image](https://user-images.githubusercontent.com/115881685/216088408-952e874e-e809-4752-a867-c3b9c48f3fdb.png)



So for Docker to push an image to ECR we first have to authenticate with a token. To get the token input the following command.


```
aws ecr get-login-password --region us-east-1
```

Copy down the output token. Now enter the following command including your URI and token!

```
aws ecr --region us-east-1| docker login -u AWS -p <encrypted_token> <repo_uri>
```


The output should show Login Successful! Now we need to tag the image so it can be pushed to the repo. To do so enter the following command using your image name and ECR repository URI.

```
docker tag docker-image <target_ecr_repo_uri>
```

Finally input the following to push the local Docker image to the remote ECR repo.

```
docker push <ecr-repo-uri>
```

It may take a minute to push the image. Let’s check AWS ECR and see if our image is there.


![image](https://user-images.githubusercontent.com/115881685/216089305-eccc161f-bf7b-41cd-a8e5-464b9787e6f5.png)


Your image should appear in the repo for future use.



In conclusion we created a custom Docker image quickly and efficiently as well as pushed it to ECR. So now you can safely remove the container and image either in the terminal or on Docker desktop.



















