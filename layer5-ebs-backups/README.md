# EBS Backup Lambda Function

The template here creates the lambda function for creating EBS backups. The template works by creating snapshots of EC2 instance volumes on a schedule provided decided by an input cron expression. Which instances to snapshot, are also decided by input parameters for the tag key and tag value of the EC2 instances.

# Using this template 

## Instructions

1. Navigate to the AWS CloudFormation service in your preferred AWS region.
2. Create Stack.
3. Choose template and upload the `ebs-backup-lambda-function.yaml` template.
4. Complete the required parameters. See `Inputs` section of the readme for more descriptive parameter definitions.
5. On successful stack creation, the lambda function for ebs backup has been created and scheduled.

### Inputs

| Parameter | Description |
|-----------|-------------|
| LambdaFunctionName | The name of the Lambda function for EBS backup. |
| EC2TagName | The key name of tag attached to the EC2 instance/s that will be backed up. | 
| EC2TagValue | The value of the tag attached to the EC2 instance/s that will be backed up. | 
| EventRuleSchedule | The Cron expression to define when the Lambda function will run. Example: 'cron(0 23 * * ? *)'. | 
| EventRuleScheduleName | The name of the event rule schedule that will be created. Example: 'run_every_day'. |
| EventRuleScheduleDescription | The description of the event rule schedule that will be created. Example: 'Runs at 2300 every day' |

### Outputs

| Output | Description |
|--------|-------------|
| EBSBackupLambdaFunctionName | The name of the Lambda function created for EBS backup |
| CloudWatchEventRuleName | Rule schedule created for EBS backup Lambda function |

# CloudFormation

The service uses the AWS CloudFormation service to provision the resource required for the EBS Backup Lambda Function

# User feedback

## Documentation
Documentation will be captured within this README.md.

## Issues
If you have any problems with or questions about this, please raise an issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

We expect you to have forked this repository in Github and modified to meet your project requirements as needed.
