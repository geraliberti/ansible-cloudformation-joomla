# ansible-cloudformation-joomla
#Deploying joomla solution in aws environment

# Here are the instructions:

#Open AWS console
#Go to Services > Cloudformation
          click "Create Stack"
          Section - Specify Template: select "Template is ready" and upload the file joomla-installation-cloudformation.yaml present in git
          Section - Specify Stack Details: write the name that will be associated to the stack being created
          Section - Configure stack options: select the following parameter:
                    Instance Type: (default is r3.4xlarge)
                    KeyName: enter "keypair1"
          Section - Review: just click create

# Assumption: - The user launching the stack above is granted role with following policies:
            CreateStack
            CreateStackInstances
            CreateStackSet
            CreateUploadBucket
            TagResource
            UpdateStack
            UpdateStackSet
            
            A key pair to access EC2 instance is present, called "keypair1"
            The estimation of load for the application has been done and 16 instances are required to afford the traffic load
            The db tables will be configured after the installation
            
#I've tried to reduce the installation to only one action (launching the cloudformation stack). The stack will deploy one ansible instance and a target EC2. The ansible instance will then launch a playbook to install the application on the target and to create an AMI from it.
#The AMI will be then used in the launch configuration associated to the autoscaling group.
 
# For the requirements of the infra i've thought about the following:
 
    Secure > security groups to allow connection only on port 443 from the internet and only port 22 from private network (for ansible and for user future modification)
    Fast > i've thought about high ram instance type and high throughput ebs volume (io1)
    Fault tolerant > autoscaling group with health check should guarantee high availability. Plus the ASG will deploy instances on 3 different AZs.
    Adaptive to average load > a cloudwatch alarm will be triggering ASG policies to scale the number of instances
