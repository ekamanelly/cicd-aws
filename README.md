


# Overview
  - this Pipeline deploys tested code on the master to AWS with a green blue strategy
  - it is Nest.js backend and React.js frontend 
  - the React app is delivered via cloudfront with s3 bucket 
  - the Nest app is on t3.maicro
  
## Built with 
  - Circle CI - Cloud-based CI/CD service
  - Amazon AWS - Cloud services
  - AWS CLI - Command-line tool for AWS
  - CloudFormation - Infrastrcuture as code
  - Ansible - Configuration management tool
  - Prometheus - Monitoring tool
  - slack

#### workflow and jobs for build and test

  There are 6 jobs that runs on any other branch except master. 

  ![build and test](/images/SCREENSHOT10.png)

  They are 

  - Build frontend
  - Scan frontend 
  - Test frontend
  - Build backend
  - Scan backend 
  - Test backend 

#### workflow and jobs to provision infrastructure and deploy artifacts 
  there are 8 jobs that works in series to deploy artifact and we highlight some of their tests 


  - deploy-infrastructure
  - configure-infrastructure
  - run-migrations
  - deploy-backend
  - deploy-frontend
  - smoke-test
  - cloudfront-update
  - cleanup


#### deploy-infrastructure 
  This job used cloud formation to create stacks on AWS. it used an image that supports aws-cli `amazon/aws-cli`, then appended Ip address to Ansible inventory file. you can see the yml files. 
  - [ec2 and security group](.circleci/files/backend.yml) 
  - [s3 bucket](.circleci/files/frontend.yml)

#### Configure-infrastructure
  We did some configuration in our newly created instance with Ansible. The environment variables like Postgres hostName and password was secretly move form circleCi over to EC2 with ansible `environment:` step

#### run-migrations
  we used a node supported image to run our application migration script and stored the outcome on  kvdb.io
  
#### deploy-backend
  we install Prometheous Exporter for monitoring, copied over the artifact to our instance, and started the app with Pm2 

#### deploy-frontend
   We append the Ip address onto `env.`, build it again and copy over the artifact to S3 with Ansible

#### smoke-test  
  Did a smoke test a status endpoint of the server and the

#### cloudfront-update
  If smoke test comes with a green status, we switch our cloudfront to point to the new S3 bucket 
  
#### - cleanup
  finally we keep the house clean by removing the previous bucket or Ec2 that failed smoke test

  thees are some screenshot from slack notification and prometheus
#### notiifcation when server in down `up==0
  ![slack ](/images/SCREENSHOT12.png)
#### free space  
  ![ ](/images/SCREENSHOT11.png)
#### cpu usage   
  ![slack ](/images/SCREENSHOT14.png)