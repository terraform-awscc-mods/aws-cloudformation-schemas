# aws-cloudformation-schemas

The Terraform AWSCC provider is dynamically built from the AWS CloudFormation schemas.  This patterns allows the provider to be updated with new resource and data source types quickly.  The schemas are not available as part of an API, but can be queries via the AWS CLI or downloaded from AWS.

This repository is a store for the schemas with a workflow to periodically download the compressed schemas for each region (since regions have different resource options) and extracts them into a directory structure that can be treated like an API.  The available schemas will then be used by the terraform-awscc-mods that are developed for input validation and documentation.

The schemas update every 24 hours.

## Usage

Query the schemas with the following:

### Shell

```shell
curl -s https://raw.githubusercontent.com/terraform-awscc-mods/aws-cloudformation-schemas/main/schemas/us-east-1/alexa-ask-skill.json
```

### Terraform

```terraform
data "http" "alexa-ask-skill" {
  url = "https://raw.githubusercontent.com/terraform-awscc-mods/aws-cloudformation-schemas/main/schemas/us-east-1/alexa-ask-skill.json"
}
```
