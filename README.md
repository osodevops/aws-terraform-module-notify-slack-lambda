[![OSO DevOps][logo]](https://osodevops.io)

# aws-terraform-module-notify-slack-lambda

This project is part of our open source DevSecOps adoption approach. 

It's 100% Open Source and licensed under the [APACHE2](LICENSE).

This module deploys and configures an SNS topic (or uses an existing one) and an AWS Lambda function that sends notifications to Slack using the [incoming webhooks API](https://api.slack.com/incoming-webhooks).

Start by setting up an [incoming webhook integration](https://my.slack.com/services/new/incoming-webhook/) in your Slack workspace.

## Solution Diagram

A diagram of the solution can be seen below: 
![solution-diagram](solution-diagram.png)

## Features

- [x] AWS Lambda runtime Python 3.8
- [x] Create new SNS topic or use existing one
- [x] Support plaintext and encrypted version of Slack webhook URL
- [x] Most of Slack message options are customizable
- [x] Support different types of SNS messages:
  - [x] AWS CloudWatch Alarms
  - [x] AWS CloudWatch LogMetrics Alarms
- [x] Local pytest driven testing of the lambda to a Slack sandbox channel

## Usage

```hcl
module "notify_slack" {
  source  = "github.com/osodevops/aws-terraform-module-notify-slack-lambda"

  sns_topic_name    = "slack-topic"

  slack_webhook_url = "https://hooks.slack.com/services/AAA/BBB/CCC"
  slack_channel     = "aws-notification"
  slack_username    = "reporter"
}
```

## Terraform versions

This module uses Terraform 0.13.

This module uses [Terraform AWS Lambda module](https://github.com/terraform-aws-modules/terraform-aws-lambda) to handle most of heavy-lifting related to Lambda packaging, roles, and permissions, while maintaining the same interface for the user of this module after many of resources will be recreated.

## Use existing SNS topic or create new

If you want to subscribe the AWS Lambda Function created by this module to an existing SNS topic you should specify `create_sns_topic = false` as an argument and specify the name of existing SNS topic name in `sns_topic_name`.

## Examples

* [notify-slack-simple](https://github.com/osodevops/aws-terraform-module-notify-slack-lambda/tree/main/examples/notify-slack-simple) - Creates SNS topic which sends messages to Slack channel.
* [cloudwatch-alerts-to-slack](https://github.com/osodevops/aws-terraform-module-notify-slack-lambda/tree/main/examples/cloudwatch-alerts-to-slack) - End to end example which shows how to send AWS Cloudwatch alerts to Slack channel and use KMS to encrypt webhook URL.

## Testing with pytest

To run the tests:

1. Set up a dedicated slack channel as a test sandbox with it's own webhook. See [Slack Incoming Webhooks docs](https://api.slack.com/messaging/webhooks) for details.
2. Make a copy of the sample pytest configuration and edit as needed.

        cp functions/pytest.ini.sample functions/pytest.ini

3. Run the tests:

        pytest functions/notify_slack_test.py

## Requirements

| Name | Version |
|------|---------|
| terraform | >= 0.13.0 |
| aws | >= 2.35 |

## Providers

| Name | Version |
|------|---------|
| aws | >= 2.35 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| lambda | terraform-aws-modules/lambda/aws | 1.28.0 |

## Resources

| Name |
|------|
| [aws_caller_identity](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/caller_identity) |
| [aws_cloudwatch_log_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_log_group) |
| [aws_iam_policy_document](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) |
| [aws_partition](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/partition) |
| [aws_region](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/region) |
| [aws_sns_topic](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sns_topic) |
| [aws_sns_topic_subscription](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sns_topic_subscription) |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| cloudwatch\_log\_group\_kms\_key\_id | The ARN of the KMS Key to use when encrypting log data for Lambda | `string` | `null` | no |
| cloudwatch\_log\_group\_retention\_in\_days | Specifies the number of days you want to retain log events in log group for Lambda. | `number` | `0` | no |
| cloudwatch\_log\_group\_tags | Additional tags for the Cloudwatch log group | `map(string)` | `{}` | no |
| create | Whether to create all resources | `bool` | `true` | no |
| create\_sns\_topic | Whether to create new SNS topic | `bool` | `true` | no |
| iam\_role\_boundary\_policy\_arn | The ARN of the policy that is used to set the permissions boundary for the role | `string` | `null` | no |
| iam\_role\_name\_prefix | A unique role name beginning with the specified prefix | `string` | `"lambda"` | no |
| iam\_role\_tags | Additional tags for the IAM role | `map(string)` | `{}` | no |
| kms\_key\_arn | ARN of the KMS key used for decrypting slack webhook url | `string` | `""` | no |
| lambda\_description | The description of the Lambda function | `string` | `null` | no |
| lambda\_function\_name | The name of the Lambda function to create | `string` | `"notify_slack"` | no |
| lambda\_function\_s3\_bucket | S3 bucket to store artifacts | `string` | `null` | no |
| lambda\_function\_store\_on\_s3 | Whether to store produced artifacts on S3 or locally. | `bool` | `false` | no |
| lambda\_function\_tags | Additional tags for the Lambda function | `map(string)` | `{}` | no |
| lambda\_function\_vpc\_security\_group\_ids | List of security group ids when Lambda Function should run in the VPC. | `list(string)` | `null` | no |
| lambda\_function\_vpc\_subnet\_ids | List of subnet ids when Lambda Function should run in the VPC. Usually private or intra subnets. | `list(string)` | `null` | no |
| lambda\_role | IAM role attached to the Lambda Function.  If this is set then a role will not be created for you. | `string` | `""` | no |
| log\_events | Boolean flag to enabled/disable logging of incoming events | `bool` | `false` | no |
| reserved\_concurrent\_executions | The amount of reserved concurrent executions for this lambda function. A value of 0 disables lambda from being triggered and -1 removes any concurrency limitations | `number` | `-1` | no |
| slack\_channel | The name of the channel in Slack for notifications | `string` | n/a | yes |
| slack\_emoji | A custom emoji that will appear on Slack messages | `string` | `":aws:"` | no |
| slack\_username | The username that will appear on Slack messages | `string` | n/a | yes |
| slack\_webhook\_url | The URL of Slack webhook | `string` | n/a | yes |
| sns\_topic\_kms\_key\_id | ARN of the KMS key used for enabling SSE on the topic | `string` | `""` | no |
| sns\_topic\_name | The name of the SNS topic to create | `string` | n/a | yes |
| sns\_topic\_tags | Additional tags for the SNS topic | `map(string)` | `{}` | no |
| subscription\_filter\_policy | (Optional) A valid filter policy that will be used in the subscription to filter messages seen by the target resource. | `string` | `null` | no |
| tags | A map of tags to add to all resources | `map(string)` | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| lambda\_cloudwatch\_log\_group\_arn | The Amazon Resource Name (ARN) specifying the log group |
| lambda\_iam\_role\_arn | The ARN of the IAM role used by Lambda function |
| lambda\_iam\_role\_name | The name of the IAM role used by Lambda function |
| notify\_slack\_lambda\_function\_arn | The ARN of the Lambda function |
| notify\_slack\_lambda\_function\_invoke\_arn | The ARN to be used for invoking Lambda function from API Gateway |
| notify\_slack\_lambda\_function\_last\_modified | The date Lambda function was last modified |
| notify\_slack\_lambda\_function\_name | The name of the Lambda function |
| notify\_slack\_lambda\_function\_version | Latest published version of your Lambda function |
| this\_slack\_topic\_arn | The ARN of the SNS topic from which messages will be sent to Slack |

## Help

**Got a question?**

File a GitHub [issue](https://github.com/osodevops/aws-terraform-module-notify-slack-lambda/issues), send us an [email][email] or tweet us [twitter][twitter].

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/osodevops/aws-terraform-module-notify-slack-lambda/issues) to report any bugs or file feature requests.

### Developing

If you are interested in being a contributor and want to get involved in developing this project or help out with our other projects, we would love to hear from you! Shoot us an [email][email].

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!

## Copyrights

Copyright © 2018-2019 [OSO DevOps](https://osodevops.io)

## License 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 

See [LICENSE](LICENSE) for full details.

    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.

## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

[![OSO DevOps][logo]][website]

We are a cloud consultancy specialising in transforming technology organisations through DevOps practices. We help organisations accelerate their capabilities for application delivery and minimize the time-to-market for software-driven innovation. 

Check out [our other projects][github], [follow us on twitter][twitter], or [hire us][hire] to help with your cloud strategy and implementation.

  [logo]: https://osodevops.io/assets/images/logo-purple-b3af53cc.svg
  [website]: https://osodevops.io/
  [github]: https://github.com/orgs/osodevops/
  [hire]: https://osodevops.io/contact/
  [linkedin]: https://www.linkedin.com/company/oso-devops
  [twitter]: https://twitter.com/osodevops
  [email]: https://www.osodevops.io/contact/
