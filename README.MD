# Running Ansible playbook using AWS Systems Manager
AWS Systems Manager lets you run Ansible Playbook on any number of servers(without Ansible Tower), without an SSH connection. We will see how to execute configuration management directives using Ansible on your instances using State Manager and Run Command, and the new `AWS-RunAnsiblePlaybook` public document.

![Fig : Serverless AWS KMS Sentry](https://raw.githubusercontent.com/miztiik/run-ansible-playbook-from-ssm/master/images/miztiik-valaxy-run-ansible-from-amazon-ssm.png)

#### Follow this article in [Youtube](https://www.youtube.com/watch?v=TLiLHwQ3kao&list=PLxzKY3wu0_FKok5gI1v4g4S-g-PLaW9YD&index=34&t=0s)

1. ## Pre-Requisities
    1. IAM Role - _i.e_ `ManagedInstanceRole` - _with managed permissions_ [Get Help for setting up IAM Role](https://www.youtube.com/watch?v=5g0Cuq-qKA0&list=PLxzKY3wu0_FLaF9Xzpyd9p4zRCikkD9lE&index=11)
        - `AmazonEC2RoleforSSM` - To allow Lambda to log events
       
1. ## Prepare Target Instances
    Lets use a RedHat 7.x Linux instances as our target instance.
    1. Assign the IAM Role created in the prerequisite
    1. Install SSM Agent
        ```sh
        sudo yum install -y https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm
        sudo systemctl enable amazon-ssm-agent
        sudo systemctl start amazon-ssm-agent
        sudo systemctl status amazon-ssm-agent
        ```
    1. Install Ansible in Redhat 7
          ```sh
          sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
          sudo yum -y install ansible
          ```
    1. Tag the instance
          ```
          TagKey:OS
          TagValue:RedHat
          ```
1. ## Create SSM State Manager
    - Choose `State Manager` from the `System Manager Services` 
    - Click on `Create Association`
    - Select the `AWS-RunAnsiblePlaybook`
    - For `Targets` Choose _Specifying tags_
    - Choose appropriate `schedule`.
    - In the _Parameters Section_, paste the playbook YAML directly.
    - Define the max errors as `1`. This means that if the execution encounters 1 `error` it will stop on the remaining targets.


1. ## Testing the solution
    Create an adhoc run using the `Run Command` manually, check the S3 Logs for execution results.

1. ## ToDo
    1. Load the playbook into CodeCommit and bring it under version control.
    1. _or_ Use S3 version control for the playbook(s) to be stored.

##### References
[1] - [AWS Docs - Ansible from SSM](https://aws.amazon.com/blogs/mt/running-ansible-playbooks-using-ec2-systems-manager-run-command-and-state-manager/)
