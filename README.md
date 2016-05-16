# aws-cf-wordpress

aws-cf-wordpress is a repo containing an AWS Cloudformation template and the Ansible playbook executed during the template initialization. 

This repository is cloned to the template's instances at ```/opt/aws-cf-wordpress```

### Expectations and assumptions

##### Expectations

- The [```aws```](https://aws.amazon.com/cli/) cli tools are installed and credentials configured
- The configuration will be deployed to us-east-1 (limitation of specifying the Instance ImageId)
- The InstanceType launched will be t2.micro (minimizing resources to fit requirements)

##### Assumptions

- The template user will provide an SSH Key name already configured in AWS
- The Output of the template run will return a URL to access the new site


#### Installation from cli

To create a stack using the *aws* cli:

First clone the repository
```
git clone https://github.com/doubtingben/aws-cf-wordpress.git
cd aws-cf-wordpress
```

Then create a stack! Make sure to specify a stack name and the ssh key for the instance(s).

```
aws cloudformation create-stack --stack-name {STACK_NAME} --parameters "ParameterKey=KeyName,ParameterValue={AWS_INSTANCE_KEY_NAME}"  --template-body file://wordpress-vpc.template
```

View the generated site by examining the *OutputKey* **URL**'s *OutputValue*

```
aws cloudformation describe-stacks --stack-name {STACK_NAME}

...
            "Outputs": [
                {
                    "Description": "Newly created application URL", 
                    "OutputKey": "URL", 
                    "OutputValue": "http://52.202.75.54"
                }
            ], 
...
```


#### Cloudfront provisioning

The ```wordpress-vpc.template``` Cloudfront template will create:

- 1 VPC, with associated network objects for default allowed access
- 1 t2.micro EC2 instance, with port 80 and 22 allowed globally

#### Ansible application installation

The ```wordpress.yml``` Ansible playbook will install ```docker``` and configure the containers required for the application.

- *mysql:5* container, with root password available on the host at ```/tmp/passwordfile```
- *wordpress:4.5-apache* container, linked to the MySQL container

#### Assumptions

- The template will be run in AWS zone us-east-1


### Todo
- Provide SSL/TLS for all HTTP traffic
- Configure cacheing for WordPress application
- Setup back up for database
- Separate application and data store to different layers to enable scaling

