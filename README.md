# aws-automatic-ebs-snapshots
This script creates automatic snapshots of ebs volume and deletes it after specified retention days.

Download this script and run first to check it works normally
```
$ bash automatic-snapshots-script.sh
```

You will need to configure AWS CLI in order to run this script using CRONJOB


**How it works:**
automatic-snapshot-script.sh will:
- Determine the instance ID of the EC2 server on which the script runs
- Gather a list of all volume IDs attached to that instance
- Take a snapshot of each attached volume
- The script will then delete all associated snapshots taken by the script that are older than 7 days

Pull requests greatly welcomed!

===================================

**REQUIREMENTS**

**IAM User:** This script requires that new IAM user credentials be created, with the following IAM security policy attached:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt14262562346560",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateSnapshot",
                "ec2:CreateTags",
                "ec2:DeleteSnapshot",
                "ec2:DescribeSnapshots",
                "ec2:DescribeVolumes"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```
<br />

**AWS CLI:** This script requires the AWS CLI tools to be installed.

First, make sure Python pip is installed:
```
# Ubuntu
sudo apt-get install python-pip -y

# Red Hat/CentOS
sudo yum install python-pip -y
```
Then install the AWS CLI tools: 
```
sudo pip install awscli
```
Once the AWS CLI has been installed, you'll need to configure it with the credentials of the IAM user created above:

```
sudo aws configure

AWS Access Key ID: (Enter in the IAM credentials generated above.)
AWS Secret Access Key: (Enter in the IAM credentials generated above.)
Default region name: (The region that this instance is in: i.e. us-east-1, eu-west-1, etc.)
Default output format: (Enter "text".)```
```
<br />

**Install Script**: Download the latest version of the snapshot script and make it executable:
```
cd ~
wget https://github.com/bhargavamin/aws-automatic-ebs-snapshots/blob/master/automatic-snapshot-script.sh
chmod +x automatic-snapshot-script.sh
mkdir -p /opt/aws
sudo mv automatic-snapshot-script.sh /opt/aws/
```
To manually test the script:
```
sudo /opt/aws/automatic-snapshot-script.sh
```

To setup a cron job in order to schedule a nightly backup. Example crontab jobs:
```
55 22 * * * root  AWS_CONFIG_FILE="/root/.aws/config" /opt/aws/automatic-snapshot-script.sh

# Or written another way:
AWS_CONFIG_FILE="/root/.aws/config" 
55 22 * * * root  /opt/aws/automatic-snapshot-script.sh
```



