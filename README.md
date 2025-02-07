# Amazon Connect Config Rules

The Amazon Connect Config Rules project contains an example of an [AWS Config Conformance Pack](https://docs.aws.amazon.com/config/latest/developerguide/conformance-packs.html) with rules for [Amazon Connect](https://aws.amazon.com/connect/) contact center instances.

This repository was presented as a demo for BIZ302, "Meet your company’s security and compliance needs with Amazon Connect" at [AWS DC Summit 2023](https://aws.amazon.com/events/summits/washington-dc/). Contained at the bottom of this repository are links to resources on Amazon Connect compliance.

**What is AWS Config?**

[AWS Config](https://aws.amazon.com/config/) is a fully managed service that provides you with resource inventory, configuration history, and configuration change notifications for AWS resources. With AWS Config, you can discover existing AWS resources, record configurations, export a complete inventory of your resources with all configuration details, and determine how a resource was configured at any point in time.

These capabilities use compliance auditing, security analysis, resource change tracking, and troubleshooting.
Show less

**What is an AWS Config rule?**

An AWS Config rule represents desired configurations for a resource and is evaluated against configuration changes on the relevant resources, as recorded by AWS Config. The results of evaluating a rule against the configuration of a resource are available on a dashboard.

Using AWS Config rules, you can assess your overall compliance and risk status from a configuration perspective, view compliance trends over time, and pinpoint which configuration change caused a resource to drift out of compliance with a rule.

**What is a conformance pack?**

A conformance pack is a collection of AWS Config rules and remediation actions that is built using a common framework and packaging model on AWS Config. By packaging the preceding AWS Config artifacts, you can simplify the deployment and reporting aspects of governance policies and configuration compliance across multiple accounts and Regions and reduce the time that a resource is kept in a non-compliant state.

## Deployment

1. Clone the repository, or download the desired conformance pack (\*.yml) CloudFormation template to your computer.
2. Navigate to the AWS CloudFormation console.
3. Choose **Create stack, with new resources (standard)**.
4. Choose **Upload a template file**. Select the above CloudFormation template from the repository.
5. For Stack name, enter something descriptive, such as `amazon-connect-config-rules_campaigns`.
6. Choose **Next**.
7. On the Configure stack options page, choose **Next**.
8. Select **I acknowledge that AWS CloudFormation might create IAM resources with custom names**.
9. Choose **Create stack**.

After the CloudFormation template has deployed, you can access the Conformance Pack and corresponding rules through the AWS Config console.

![screenshot.png](screenshot.png)

## What did I just deploy?

The rules contained here are meant to provide examples of how you can use AWS Config to automate security and compliance checks on your contact center instances. For example, a rule was deployed to ensure that if your Amazon Connect instance has Call Recording enabled, you are using AWS KMS encryption keys. The rule will mark resources (Amazon Connect instances) **Compliant** if this is true, **Not Compliant** if there is no encryption, or **Not Applicable** if call recording is disabled.

## Description of various samples in this repository

**campaigns.yml**

This Conformance Pack for AWS Config demonstrates an evaluate Amazon Connect configuration in relation to campaigns. It checks various configuration components of all Amazon Connect instances such as flows and lambda functions to ensure best practices for campaigns are being followed.

**check-connect-lambda-timeout-setting.yml**

This Conformance Pack for AWS Config demonstrates an evaluation the lambdas configured for all connect instances, and checks to ensure they have a maximum timeout of 8 seconds configured (which is the timeout for an invoke lambda function flow block)

**check-connect-log-group-retention-period.yml**

This Conformance Pack for AWS Config demonstrates an evaluation if the log retention period for amazon connect cloudwatch logs has been set and that it is set to a minimum setting. When you use an S3 bucket or CloudWatch log group to store logs, you must establish adequate lifecycles for each log source to optimize storage and retrieval costs. Customers generally have between 3 and 12 months of logs readily available for querying, with retention of up to seven years. The choice of availability and retention should align with your security requirements and a composite of statutory, regulatory, and business mandates. By default this checks for a minimum of 120 days, however can be edited for higher retention periods by changing the MINIMUM_RETENTION_IN_DAYS_FOR_COMPLIANCE value in the pack.

**check-hoursofoperation-staffing.yml**

This Conformance Pack for AWS Config demonstrates an evaluation to ensure flows use check staffing blocks prior transfering a contact to a queue.

**check-orphan-agent-quickconnects.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of agent quick connects to ensure they point to actual configured users and are not orphaned.

**data-storage-region-compliance.yml**

This Conformance Pack for AWS Config demonstrates an evaluationif various Amazon Connect Data Storage Resources is in the Same AWS region as the Amazon Connect Contact Center instance. For example "GDPR" is a European Union regulation which mandates data to reside within Europe itself.

Note: Did not include evaluation for any Kinesis Storage Type since Kinesis based integrations cannot be configured cross region via UI or APIs.

**external-transfers.yml**

This Conformance Pack for AWS Config demonstrates evaluates the phone number parameter configuration within the "Transfer to phone number" blocks in Amazon Connect contact flows. When setting up phone number transfers in contact flows, it's crucial that the phone numbers are properly formatted to ensure successful call transfers and maintain security standards. The pack validates that all phone numbers specified in these transfer blocks strictly adhere to the E.164 international format (e.g., +12065550100), when statically configured. Improper phone number formatting in transfer blocks can lead to failed transfers, customer experience issues, and potential security vulnerabilities. The pack scans all contact flows in the Connect instance, identifies "Transfer to phone number" blocks, and verifies their phone number parameter value. Regular validation of these transfer blocks helps maintain operational reliability and ensures compliance with telecommunications standards.

**general-security-checks.yml**

This pack demostrates several general security conditions such as s3 bucket encyption.

**iam-bucket-and-policy.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of Amazon Connect instances associated buckets have the appropriate IAM bucket policy (minimum required policy) permissions for Amazon Connect to access the S3 buckets. Also, the Conformance pack evaluates if the bucket is publicly accessible or not.

**lex-v2-resource-policy.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of the Amazon Lex V2 resource policy to ensure it is properly configured and verifies that the associated Amazon Connect instance has the necessary permissions to access it. When integrating Amazon Connect with Lex V2 chatbots, proper resource-based policies must be established to ensure secure communication between the services. The pack validates that the Lex V2 bot's resource policy explicitly grants the required permissions to the Connect instance's service principal, preventing unauthorized access while maintaining service functionality.

**loop-prompt-interruption.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of set interruption times of Loop Prompt flow blocks is less than 20 secs on Amazon Connect instance.
As mentioned in the Loop Prompts best practices(https://docs.aws.amazon.com/connect/latest/adminguide/loop-prompts.html#loop-prompts-tips), always use an interruption period that's greater than 20 seconds. This is the amount of time an available agent has to accept the contact. If the interruption period is less than 20 seconds, you might get contacts going down the Error branch. This is because Amazon Connect doesn't support dequeuing the customer when they are being routed to an active agent and are in the 20 second window to join.

**\*queue-callerid.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of outbound caller ID numbers on Amazon Connect instance Queues. There is one rule which checks whether all the queues in Amazon Connect instances has callerid number set. There is another rule which checks whether only the queues which are set as Outbound Queue in Routing Profile has callerid number set.
An Amazon Connect Agent who makes an outbound call and does not have an outbound caller ID set can have a call initiated with caller ID as "anonymous". This can cause unexpected results like call failures or incorrect caller id displayed.

**s3-accesspolicy-check.yml**

This conformance pack evaluate the S3 buckets associated to the Amazon Connect instances for the access policy and ensure that it is abiding by the best practices.

**s3-objectlock-check.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of s3 object locks to ensure they are configured for all Amazon Connect associated s3 buckets.

**store-customer-input-encryption.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of the Get Customer Input Block has Encryption enabled for "custom inputs".
As mentioned in the best practices for PII compliance in Amazon Connect(https://docs.aws.amazon.com/connect/latest/adminguide/compliance-validation-best-practices-PII.html), Use encryption in the Store Customer Input Block for sensitive DTMF information.

**unassigned-queues.yml**

This Conformance Pack for AWS Config demonstrates an evaluation of finding Queues which are not assigned to any Amazon Connect Routing Profiles.
Any unassigned queues will be an Orphan Resource and hence would be good to be removed if not in use.

**unassigned-quick-connects.yml**

This Conformance Pack for AWS Config demonstrates an evaluation if there are any Quick Connects which are not assigned to any Amazon Connect Queues.
Any Unassigned Quick Connects will be an Orphan Resource and hence would be good to be removed if not in use.

**unassigned-routing-profiles.yml**

This Conformance Pack for AWS Config demonstrates an evaluation if there are any Routing Profiles which are not assigned to any Amazon Connect agents.
Any unassigned Routing Profiles will be an orphaned resource and hence would be good to be removed if not in use.

## What next?

AWS Config Custom Rules can be designed to check additional configurations made on your Amazon Connect instance. For example, you may want to continuously audit security profies, phone number configurations, etc. Additional conformancee packs can be deployed to provide starting points on operational best practices for common compliance frameworks like HIPAA, PCI, and FedRAMP.

While this conformance pack is not designed to ensure compliance by itself, you can use patterns laid out here to identify non-compliant and automatically remediate issues as defined by your organization.

## How can I contribute?

If you have examples of AWS Config rules for Amazon Connect instances, feel free to open a PR with your suggestions and contributions!

## Additional Resources

- [Amazon Connect Docs: Best practices for PII compliance in Amazon Connect](https://docs.aws.amazon.com/connect/latest/adminguide/compliance-validation-best-practices-PII.html)
- [Creating a Secure IVR solution with Amazon Connect blog](https://aws.amazon.com/blogs/contact-center/creating-a-secure-ivr-solution-with-amazon-connect/)
- [Amazon Connect achieves FedRAMP High authorization](https://aws.amazon.com/blogs/publicsector/amazon-connect-achieves-fedramp-high-authorization/)
- [AWS Services in Scope by Compliance Program page](https://aws.amazon.com/compliance/services-in-scope/)

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.
