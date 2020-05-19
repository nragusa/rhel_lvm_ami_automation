## Overview
Converts a source RHEL 7.x AMI to one that leverages LVM. Creates a new AMI with the following partition scheme:

```
UUID=12345-abcd                 /boot   ext4    defaults        1 2
/dev/mapper/rhel-root           /       xfs     defaults        0 0
/dev/mapper/rhel-opt            /opt    xfs     defaults        0 2
/dev/mapper/rhel-tmp            /tmp    xfs     defaults        0 2
/dev/mapper/rhel-home           /home   xfs     defaults        0 2
/dev/mapper/rhel-var            /var    xfs     defaults        0 2
/dev/mapper/rhel-var_tmp        /var/tmp        xfs     defaults        0 2
/dev/mapper/rhel-var_log        /var/log        xfs     defaults        0 2
/dev/mapper/rhel-var_log_audit  /var/log/audit  xfs     defaults        0 2
```

## Pre-requisites
Before running this automation document, you must have a ServiceRole created for Systems Manager. 

1. Follow these instructions to [Create the service role using AWS CloudFormation](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-cf.html#automation-cf-create).
1. Once complete, you add an inline policy to the ServiceRole. Deploy this [CloudFormation template](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=SSMAutomationRolePolicy&templateURL=https://ragusan-cloudformation.s3.amazonaws.com/automation_policy.yml) to create the policy.

## Running the Automation

There are a few parameters you can specify.

#### Required

1. **SourceAmiId** This has been tested with `ami-0170fc126935d44c3
`, a RHEL 7.7 image in us-east-2. Be sure to find an appropriate source AMI in your working region.
1. **IamInstanceProfileName** The default should suffice, assuming you satisfied the pre-requisites from above.
1. **AutomationAssumeRole** The default should suffice, assuming you satisfied the pre-requisites from above.

#### Optional

1. **SubnetId** Make sure instances in this subnet can communicate with SSM endpoints (either a public subnet, a subnet that uses a NAT gateway, or SSM endpoints via PrivateLink). If note specified, it will use a subnet from the default VPC in the same AZ as the EC2 instance.
1. **LVMVolumeSize** Specify a size, in GiB, between 10 and 16384. Recommended size of 50 or greater.