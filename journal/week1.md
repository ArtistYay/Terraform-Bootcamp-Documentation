# Terraform Beginner Bootcamp 2023 - Week 1

![screenshot](../assets/Week_1_diagram.png)

# Table of contents

- [Restructure Root Module](#restructure-root-module)
- [Terraform Import & Configuration Drift :wind_face:](#terraform-import--configuration-drift-wind_face)
- [Creating a Terra:house: Module](#creating-a-terrahouse-module)
- [Static Website Hosting](#static-website-hosting)
- [Content Delivery Network](#content-delivery-network)
- [Invalidate Cache and Local Exec](#invalidate-cache-and-local-exec)
- [Assets Upload and For Each](#assets-upload-and-for-each)
- [References](#references)

## Restructure Root Module

- A best practice is to have your files in a Standard Module Structure[<sup>[1]</sup>](#references). A standard module structure helps to ensure that all Terraform modules are structured in a consistent way, makes it easier to reuse modules in different projects, and makes Terraform modules easier to maintain and update.

```
PROJECT_ROOT
│
├── main.tf                 # everything else.
├── variables.tf            # stores the structure of input variables.
├── terraform.tfvars        # the data of variables we want to load into our terraform project.
├── providers.tf            # defined required providers and their configuration.
├── outputs.tf              # stores our outputs.
└── README.md               # required for root modules.
```
- We added tags to our S3 bucket. The tag calls a variable from our `variables.tf` file which also validates[<sup>[14]</sup>](#references) the format.

```
variable "user_uuid" {
  description = "The UUID of the user"
  type        = string
  validation {
    condition        = can(regex("^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[1-5][0-9a-fA-F]{3}-[89abAB][0-9a-fA-F]{3}-[0-9a-fA-F]{12}$", var.user_uuid))
    error_message    = "The user_uuid value is not a valid UUID."
  }
}
```
- In Terraform Cloud you can set two types of variables[<sup>[16]</sup>](#references):

  - Environment Variables: store provider credentials and other data that is needed to run Terraform operations.
  - Terraform Variables: used to customize Terraform configurations.
 
- The difference between environment variables and Terraform variables is that environment variables are used to store data that is needed to run Terraform, while Terraform variables are used to customize Terraform configurations.

- We can use the `-var` flag to set an input variable or override a variable in the tfvars file eg. `terraform -var user_ud="my-user_id"`

- The `-var-file flag` is a Terraform command-line option that allows you to pass the values of variables to Terraform from a file. This can be useful for passing sensitive data, such as provider credentials, to Terraform in a secure way.

```
terraform apply -var-file="variables.tfvars"
```
> Passing the values of the `aws_access_key_id` and `aws_secret_access_key` variables from a file called `variables.tfvars`.

- `.terraform.tfvars` is the default file to load in terraform variables in bulk.

- The `auto.tfvars` file functionality in Terraform Cloud is a way to automatically load variable values from a file into your Terraform configuration. This can be useful for setting variables that are common to multiple workspaces, or for setting variables that contain sensitive data. To use the `auto.tfvars` file in a Terraform Cloud workspace, simply add the file to the workspace's Variable Files section.

- Terraform loads variables in the following order[<sup>[17]</sup>](#references):
```
├── Environment variables                 
├── terraform.tfvars            
├── terraform.tfvars.json        
├── *.auto.tfvars/*.auto.tfvars.json            
└── -var/-var-file  
```

## Terraform Import & Configuration Drift :wind_face:

- Terraform import is a command that allows you to bring existing infrastructure under Terraform management. For example, you created an EC2 instance and want Terraform to manage it in the state file. You can use the command `terraform import aws_instance.my_instance i-1234567890abcdef0`[<sup>[18]</sup>](#references) or use the import code block[<sup>[3]</sup>](#references):

```
import {
  to = aws_instance.example
  id = "i-abcd1234"
}

resource "aws_instance" "example" {
  name = "hashi"
  # (other resource arguments...)
}
```
- The `terraform import` command will create a new resource block in your Terraform configuration file for the imported resource. You can then manage the resource like any other Terraform-managed resource.

## Creating a Terra:house: Module

- It is recommend to place modules in a `modules` directory when locally developing modules but you can name it whatever you like.

- We can pass input variables to our module. The module has to declare the terraform variables in its own variables.tf.

```
module "terrahouse_aws" {
  source = "./modules/terrahouse_aws"
  user_uuid = var.user_uuid
  bucket_name = var.bucket_name
}
```
- Using the source[<sup>[4]</sup>](#references) we can import the module from various places eg:
  - locally
  - Github
  - Terraform Registry

```
module "terrahouse_aws" {
  source = "./modules/terrahouse_aws"
}
```

## Static Website Hosting

- AWS let's you host a static website on a S3 bucket. You can use HTML, CSS, Javascript.

- You can use Terraform to push HTML files to the bucket using `resource "aws_s3_object"`:

```
resource "aws_s3_object" "index_html" {
  bucket = aws_s3_bucket.website_bucket.bucket
  key    = "index.html"
  source = var.index_html_filepath

  etag = filemd5(var.index_html_filepath)
}
```
> We used the `etag`[<sup>[6]</sup>](#references) and `filemd5` function[<sup>[7]</sup>](#references) to get the hash of the file so if any changes was made to the file Terraform can replace the object in the bucket.

- For the source, you can use a special variable called `path`[<sup>[5]</sup>](#references) to reference local paths:
  - `path.module` = get the path for the current module.
  - `path.root` = get the path for the root module.
 
```
resource "aws_s3_object" "error_html" {
  bucket = aws_s3_bucket.website_bucket.bucket
  key    = "error.html"
  source = "${path.root}/public/index.html"

  etag = filemd5(var.error_html_filepath)
}
```

- We also used the `fileexist` function to check if the file exist.

```
variable "error_html_filepath" {
  description = "The file path for error.html"
  type        = string

  validation {
    condition     = fileexists(var.error_html_filepath)
    error_message = "The provided path for error.html does not exist."
  }
}
```
## Content Delivery Network 

- AWS no longer uses OAI but OAC[<sup>[9]</sup>](#references)

- We use the jsonencode to create the json policy inline in the HCL.

```
> jsonencode({"hello"="world"})
{"hello":"world"}
```

## Invalidate Cache and Local Exec

- `local-exec`[<sup>[13]</sup>](#references) will execute command on the machine running the terraform commands eg. plan apply

```
resource "aws_instance" "web" {
  # ...

  provisioner "local-exec" {
    command = "echo The server's IP address is ${self.private_ip}"
  }
}
```
## Assets Upload and For Each

- For each[<sup>[12]</sup>](#references) allows us to enumerate over complex data types

```sh
[for s in var.list : upper(s)]
```

This is mostly useful when you are creating multiples of a cloud resource and you want to reduce the amount of repetitive terraform code.

## References

- [Standard Module Structure](https://developer.hashicorp.com/terraform/language/modules/develop/structure)<sup>[1]<sup>

- [GitHub Wiki TOC generator](https://luciopaiva.com/markdown-toc/)<sup>[2]<sup>

- [Terraform Import](https://developer.hashicorp.com/terraform/language/import#resource-configuration)<sup>[3]<sup>

- [Terraform Module Sources](https://developer.hashicorp.com/terraform/language/modules/sources)<sup>[4]<sup>

- [Terraform References to Named Values](https://developer.hashicorp.com/terraform/language/expressions/references#filesystem-and-workspace-info)<sup>[5]<sup>

- [ETag](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag)<sup>[6]<sup>

- [FileMD5 Function](https://developer.hashicorp.com/terraform/language/functions/filemd5)<sup>[7]<sup>

- [Terraform Lifecycle](https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle)<sup>[8]<sup>

- [AWS OAC](https://aws.amazon.com/blogs/networking-and-content-delivery/amazon-cloudfront-introduces-origin-access-control-oac/)<sup>[9]<sup>

- [Terraform Strings & Templates](https://developer.hashicorp.com/terraform/language/expressions/strings)<sup>[10]<sup>

- [HTTP-Server](https://www.npmjs.com/package/http-server)<sup>[11]<sup>

- [Terraform For_Each](https://developer.hashicorp.com/terraform/language/meta-arguments/for_each)<sup>[12]<sup>

- [Terraform Local-Exec](https://developer.hashicorp.com/terraform/language/resources/provisioners/local-exec)<sup>[13]<sup>

- [Terraform Fileset Function](https://developer.hashicorp.com/terraform/language/functions/fileset)<sup>[14]<sup>

- [Terraform Custom Validation Rules](https://developer.hashicorp.com/terraform/language/values/variables#custom-validation-rules)<sup>[15]<sup>

- [Terraform Workspace Variables](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/variables)<sup>[16]<sup>

- [Terraform Variable Definition Precedence](https://developer.hashicorp.com/terraform/language/values/variables#variable-definition-precedence)<sup>[17]<sup>

- [Terraform Command: import](https://developer.hashicorp.com/terraform/cli/commands/import)<sup>[18]<sup>
