# ECS_ECR
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


  
