# aws-cloudformation-schemas

The Terraform AWSCC provider is dynamically built from the AWS CloudFormation schemas.  This patterns allows the provider to be updated with new resource and data source types quickly.  The schemas are not available as part of an API, but can be queries via the AWS CLI or downloaded from AWS.

This repository is a store for the schemas with a workflow to periodically download the compressed schemas for each region (since regions have different resource options) and extracts them into a directory structure that can be treated like an API.  The available schemas will then be used by the terraform-awscc-mods that are developed for input validation and documentation.

The schemas update every 24 hours.

## Usage

Query the schemas with the following:

### Shell

The following command will query the schema via curl and parse it via jq.  This can be useful to get the available options for a specific property.

```shell
curl -s https://raw.githubusercontent.com/terraform-awscc-mods/aws-cloudformation-schemas/main/schemas/us-east-1/aws-lambda-function.json | jq -r '.properties.PackageType'
```

### Terraform

As of Terraform 1.9, variable validation can support cross type references.  This essentially means that the previous limitation that a variable validation block could only reference the variable itself is no longer a limitation.  This allows for the use of the `http` data source to query the schema and use it for validation.

```terraform
data "http" "package_type" {
  url = "https://raw.githubusercontent.com/terraform-awscc-mods/aws-cloudformation-schemas/main/schemas/us-east-1/aws-lambda-function.json"
}

locals {
  validation_package_type = jsondecode(data.http.package_type.response_body).properties.PackageType
}

variable "package_type" {
  default     = "Zip"
  description = "The type of package to install"
  type        = string

  validation {
    condition     = contains(local.validation_package_type.enum, var.package_type)
    error_message = local.validation_package_type.description
  }
}

output "package_type" {
  value = var.package_type
}
```
