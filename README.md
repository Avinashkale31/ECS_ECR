# Deploy application Using ECR ECS and Automate using CI/CD.
1.	Create a EC2 Instance use Ubuntu AMI Image and Connect to instance – configure security group 3000 port no – ssh 22 to connect.
![image](https://github.com/user-attachments/assets/55f6b6f6-6fc0-4817-b796-38e5172a1f14)

2.	Prerequisites in instance
-	Install AWS CLI
-	Install Docker
-	Create a IAM user – attach policy - AmazonEC2ContainerRegistryFullAccess.
-	Create Security Credential of user 
-	Configure in instance using command $ aws configure
-	Create a security group which will have inbound rules 3000, 80, 443 – source (anywhere).

![image](https://github.com/user-attachments/assets/cc1299c0-89bc-4ab6-8885-05328e017526)

3.	Clone the github repository
   $ git clone https://github.com/Avinashkale31/ECS_ECR.git

   ![image](https://github.com/user-attachments/assets/b27958f1-e667-4009-92b1-df3d4adf0395)

 4.	First Create the Image and Container. Check Application
-	Go to ECS_ECR directory
-	Build docker image $ sudo docker build .
-	Check the image $ sudo docker images
-	Run the container $ sudo docker run -dp 3000:3000 <ImageID>
-	Copy the Public IP of instance and hit in the brower <Public IP:3000>

  ![image](https://github.com/user-attachments/assets/4e0481bf-13ee-4dc8-aafe-a8f436eb2a28)

5.	Now create ECR
-	Go to ECR service – private repository – give name – create.

![image](https://github.com/user-attachments/assets/601fca0a-fbec-4fef-8610-c7145909b47d)

6.	Push image To ECR
-	Select the repository – view push command – copy the commands – paste in the instance in ECS_ECR directory.   If command not works add sudo at beginning of command 

![image](https://github.com/user-attachments/assets/9544401c-7622-4f8a-83a4-31564ccc48de)

7.	Create a Database

   ![image](https://github.com/user-attachments/assets/071edd53-386c-4d70-a7a1-49840937cd89)

8.	Create a S3 bucket to sore credential of s3 bucket.

   ![image](https://github.com/user-attachments/assets/6ce848da-469e-4953-b916-815dce161c5d)
-	Create a file to store to credential of rds, always create file with extension  .env (eg:- filename.env).  same as the syntax
{
  "DB_HOST": "DB Endpoint",
  "DB_USER": "DB username",
  "DB_PASSWORD": "password",
  "DB_NAME": "DB name"
}

![image](https://github.com/user-attachments/assets/87df2b5c-f0d5-406f-9f3d-fa2426cf8f7a)

9.	Now Create ECS Cluster
-	Go to ECS Service – create cluster – name – choose AWS Fargate  - create.

10.	Create Task Definitions

![image](https://github.com/user-attachments/assets/2a2ab74a-25ce-4ade-b170-effa4ae9d2b9)

![image](https://github.com/user-attachments/assets/a766aab1-26bf-4625-85be-02fb0951ecc8)

-	In Environment variable paste the image ARN from the S3 bucket.
  ![image](https://github.com/user-attachments/assets/eccdef41-a59d-4e00-ab6c-08d0dc24bc19)
-	After creating a Task definition – go to task – select task – select Task execution role - which will redirect to role – add permission – inline policy – choose service ECS - Jason edit – paste the policy.
-	To access the S3 bucket.
```bash
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::<account>-<account-id>/env-demo.env"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::<account>-<account-id>"
            ]
        }
    ]
}
```

-	Change the account id and bucket name.
11.	Create Service
-	Go the cluster – create service
![image](https://github.com/user-attachments/assets/490f7ff3-2618-4f5c-9884-b7277ab94de9)
![image](https://github.com/user-attachments/assets/41ca4561-6a5b-4a1a-8dad-bce5c1476964)
![image](https://github.com/user-attachments/assets/dc96ad59-7730-4247-a825-0c2f351cfd51)

-	Attach security group which we have created at starting.

  ![image](https://github.com/user-attachments/assets/2771333d-afc4-4639-94d0-316b6d28674c)

![image](https://github.com/user-attachments/assets/4b81a4d4-5a7c-42c2-996d-a1d2a54f9fce)

-We can see Service Is in Running Condition.

![image](https://github.com/user-attachments/assets/8759f11d-428c-46d8-a7f4-cedc27345eef)

-	We can Check the application using the load balancer DNS
![image](https://github.com/user-attachments/assets/95eaaa35-508d-4a58-a865-48ade9f992ed)

12.	Now we have to route traffic to domain to access with domain.
-	Go to route 53 – register a domain or register from third party.
-	So, I am using a domain from third party.
-	Create a hosted zone – paste the name server – to third party name server
-	So, it will route the traffic to that domain.

![image](https://github.com/user-attachments/assets/844a0c2e-eae8-4b89-bc31-70e3fdb84c26)

13.	Now Create an SSL certificate to access application securely.
-	Go to ACM service – request – Public certificate – give domain name – DNS validation.
![image](https://github.com/user-attachments/assets/7a1ed0b1-a48c-42d0-b8d7-d1a7a0f3db40)

-	Now go to certificate – create Record in route 53 - it will be issued after creating a record and we can see new record will be created in route 53.

14.	Now configure the SSL certificate in the Load Balancer.
-	Go to the load balancer – select the which load balancer has created through Service.
-	Go to the Listeners and rules – create new listener.

![image](https://github.com/user-attachments/assets/2edd5d47-07eb-4a65-9148-9926ce6fe1a6)
![image](https://github.com/user-attachments/assets/24a6f307-7f34-47cf-af79-f857d64c1694)

-	Select ACM which we have created and target group.
-	Now come back to the load balancer – go to listener and rule – select http 80 - edit http 80.

![image](https://github.com/user-attachments/assets/5acde564-ad89-4c90-a56b-5cc4c6b69d29)


-	It will redirect request 80 to 443, because 443 is secure.
-	Now we can access the application with domain name.
-	See the application is now secure.

![image](https://github.com/user-attachments/assets/7ec84285-c30e-48ea-b47c-0dc1009f60e8)

15.	Now Automate the process AWS CodePipeline and CodeBuild
-	Go to Codepipeline – create – select build custom pipeline

![image](https://github.com/user-attachments/assets/91c9d0a1-fa17-45f2-aa46-864fe227f6c2)
![image](https://github.com/user-attachments/assets/1d1ea0fe-18eb-444a-9145-1e1c6f96ac3f)

-	Connect to the GitHub

![image](https://github.com/user-attachments/assets/9485797e-cba9-4ad1-aae2-aa4f85893039)
-	Select repository – give branch name.

![image](https://github.com/user-attachments/assets/0af3d064-8743-41c3-86af-b21f5b0f56f9)

![image](https://github.com/user-attachments/assets/ff6d9dbe-7fc0-4401-81e0-0abc3299aa37)

-	Create Project

![image](https://github.com/user-attachments/assets/8499e13d-9b6d-465b-8fe4-d2a49ac81b5a)
![image](https://github.com/user-attachments/assets/9091de7d-fc7d-4fe7-9ed6-b720479336de)
![image](https://github.com/user-attachments/assets/f4e77416-d8d0-4686-b3f1-c5aa30e4882d)

-	Select use buildspec file – give file name buildspec.yml
-	By this CodeBuild will be created.
-	Now, go to Code Build – select build which we created – go to service role – add permission - AmazonEC2ContainerRegistryFullAccess
-	
-	Give the Build name which we created in the project name
![image](https://github.com/user-attachments/assets/07f3571a-52da-4a51-a469-c62a111e87db)
![image](https://github.com/user-attachments/assets/94482c59-67d2-4a01-a9c3-afc29cd5d776)

-	Add environment variable – names will from buildspec.yml file and value from the ECR repo, container name from task.

![image](https://github.com/user-attachments/assets/5076b5c7-2afa-47d0-8337-05e4706ded5f)
![image](https://github.com/user-attachments/assets/82f12670-6efe-4e5a-be71-9606c1ebcf1d)

-	Create Deploy 
-	Select service, cluster, and image definitions copy from the buildspec.yaml file at the end of the file and paste here.

-	We can see the pipeline is success.
![image](https://github.com/user-attachments/assets/ad4541f2-4caa-4170-a847-5aab25804168)

![image](https://github.com/user-attachments/assets/1f0a51d9-c241-46ab-bfa1-b0e5d944300a)

-	Now connect to the instance – go to the repository directory – go to the html directory – change title in the index.html file – and push the file to the github.
-	After changing in the file push the code using this command.
-	$ git add .
-	$ git commit -m “new”
-	$ git push -u origin main

-we can see the pipeline is trigger automatically.
![image](https://github.com/user-attachments/assets/dbec9234-a0fc-42ee-a2e0-450f253e49bf)

-	We can see there are changes in the Application.

![image](https://github.com/user-attachments/assets/f5e127c2-67f8-4b94-af5e-f12611cf8e8c)

Conclusion: We have successfully deployed an application using ECR and a CI/CD pipeline. Additionally, we utilized ACM to create an SSL certificate, Route 53 to route traffic to the domain, and S3 to securely store credentials.
