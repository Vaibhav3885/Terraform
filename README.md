# Terraform

### ✅ 1. What Is Terraform?
Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp to provision, manage, and version cloud infrastructure across multiple providers like AWS, Azure, and GCP.

### ✅ 2. Terraform Workflow (Basic to Advanced)
Step	Command	Purpose
Initialize	terraform init	Downloads required providers & modules
Plan Changes	terraform plan	Shows what will be created/changed
Apply Changes	terraform apply	Provisions infra as per .tf files
Destroy Resources	terraform destroy	Deletes the infrastructure
Format Code	terraform fmt	Formats Terraform code
Validate Code	terraform validate	Validates .tf syntax

### ✅ 3. Terraform File Types
File	Purpose
main.tf	Main infrastructure configuration
variables.tf	Input variables
outputs.tf	Outputs after deployment
terraform.tfvars	Variable values for environments
backend.tf	(optional) Remote state configuration

### ✅ 4. Providers & Resources
🔹 Provider Example (AWS):

```
provider "aws" {
  region = "us-east-1"
}
```
🔹 Resource Example (EC2):
```

resource "aws_instance" "my_vm" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = "web-server"
  }
}
```

### ✅ 5. Variables & Outputs
🔹 variables.tf
```
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}
```
🔹 terraform.tfvars

```
instance_type = "t3.micro"
```
🔹 outputs.tf
```
output "public_ip" {
  value = aws_instance.my_vm.public_ip
}
```
### ✅ 6. Terraform State Management
Concept	Description
Local State	Stored as terraform.tfstate locally
Remote State	Store state in S3, GCS, or Azure Blob
State Locking	Use DynamoDB (AWS) to prevent concurrent apply
Sensitive Data	Don’t commit state files; encrypt state at rest

🔹 Remote Backend Example (AWS):
```
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/vpc.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
    encrypt        = true
  }
}
```

### ✅ 7. Terraform Modules (Very Important)
Purpose	Make code reusable, modular, and clean
Example Modules	vpc, eks, alb, rds, s3

🔹 Calling a Module
```
module "vpc" {
  source = "./modules/vpc"
  cidr_block = "10.0.0.0/16"
}
```
🔹 Directory Structure
```
.
├── main.tf
├── variables.tf
├── modules/
│   └── vpc/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
```

### ✅ 8. Terraform Lifecycle & Meta-Arguments
Feature	Use Case
depends_on	Control resource creation order
lifecycle	Manage create_before_destroy, prevent_destroy, etc.
count / for_each	Create multiple instances dynamically

🔹 Example:
```
resource "aws_instance" "web" {
  count         = 3
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

### ✅ 9. Terraform with CI/CD
Run Terraform commands using Jenkins, GitHub Actions, or GitLab

Use Terraform Cloud or Atlantis for auto-approval workflows

Example Jenkins pipeline:

```
stage('Terraform Plan') {
  steps {
    sh 'terraform init'
    sh 'terraform plan -out=tfplan'
  }
}
stage('Terraform Apply') {
  steps {
    input "Approve deployment?"
    sh 'terraform apply tfplan'
  }
}
```

### ✅ 10. Terraform Security Best Practices
Best Practice	Details
Don’t hardcode secrets	Use env variables or Vault
Use .gitignore for tfstate	Never commit terraform.tfstate or .tfvars
Use remote state	S3 + DynamoDB for AWS projects
Scan Terraform for issues	Tools: tfsec, Checkov, TFLint
Least privilege IAM for Terraform	Don't give Terraform admin-wide permissions

### ✅ Interview-Specific Terraform Questions
Question	What They’re Looking For
How do you manage state files in a team?	
Use remote backend with locking (S3 + DynamoDB)

What is the use of modules?	
Reusability and cleaner infrastructure code

How do you secure secrets in Terraform?	
Vault, env vars, ignore tfstate in Git

How do you integrate Terraform in CI/CD?	
Jenkins pipeline, approval gates

What is the difference between count and for_each?	
count is index-based, for_each is key-based

Have you used data sources?	
Read existing cloud data without managing it

#### Q: What is Terraform and how is it different from other IaC tools?
A: Terraform is a tool that allows us to define infrastructure using code and manage it declaratively. Unlike tools like Ansible or Chef which are imperative, Terraform uses a state file to manage what exists, what changed, and what should be applied.

#### Q: What happens during terraform init and terraform plan?
A: init initializes the directory, downloads provider plugins.
plan checks current state vs desired state and shows what changes will be made, without applying them.

#### Q: How do you structure Terraform projects for different environments?
A: We separate code into modules and use different .tfvars files for dev, stage, and prod. We pass them with -var-file flag.

#### Q: What is a provider in Terraform?
A: A provider is a plugin that allows Terraform to interact with cloud services like AWS, Azure, or GCP.

#### Q: How do you pass values to Terraform?
A: Through terraform.tfvars files or command-line flags like -var="key=value".

#### Q: What is a state file and why is it important?
A: The state file is how Terraform tracks real-world infrastructure. It enables change detection and safe updates. Without it, Terraform can’t manage existing resources correctly.

##### Q: Why do you use modules in Terraform?
A: For code reuse, clarity, and to follow DRY principles. It makes infra management easier across multiple environments.

#### Q: How do you secure Terraform projects?
A: Avoid hardcoded secrets, use vaults or environment variables, store state securely in S3, and use tools like tfsec for IaC scanning

## Terraform Interview Questions & Best Answers

#### 🔸 Q1. What is Terraform and why do we use it?
Answer:
Terraform is an open-source Infrastructure as Code (IaC) tool created by HashiCorp.
It is used to create, manage, and automate cloud infrastructure like EC2, VPC, S3, RDS, etc., using code instead of manual clicks.

✅ Why use it?

Automates infrastructure setup

Supports multi-cloud (AWS, Azure, GCP)

Tracks changes via Git (version control)

Reusable and consistent infra

Supports modularity and teamwork

#### 🔸 Q2. How does Terraform work?
Answer:

Terraform follows 3 main steps:

terraform init – Downloads the cloud provider plugin (like AWS).

terraform plan – Shows what will change in the cloud.

terraform apply – Actually creates or modifies the resources.

terraform destroy – Deletes the resources.

Terraform keeps track of all infra in a file called terraform.tfstate.

####  Q3. What are Providers in Terraform?
Answer:
Providers are plugins that let Terraform communicate with different cloud platforms.

👉 Example:

```
provider "aws" {
  region = "us-east-1"
}
```
This tells Terraform to use AWS and deploy resources in the us-east-1 region.

#### 🔸 Q4. What is a Resource in Terraform?
Answer:
A resource is any cloud object like an EC2, S3, VPC, etc.

👉 Example:

```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```
Here, you're launching an EC2 instance using Terraform.

#### 🔸 Q5. What are Variables in Terraform?
Answer:
Variables make your code flexible and reusable.

👉 Declare:

```
variable "region" {
  default = "us-east-1"
}
```
👉 Use:

```
provider "aws" {
  region = var.region
}
```
You can pass variable values through terraform.tfvars or command line.

#### 🔸 Q6. What is the Terraform State File?
Answer:
Terraform uses a file called terraform.tfstate to track the current state of your infrastructure.

⚠️ If someone changes the infra manually in AWS, Terraform won’t know unless you refresh the state.

In teams, you store the state file in remote backend like S3 + DynamoDB for locking.

#### 🔸 Q7. What are Terraform Modules?
Answer:
Modules are reusable blocks of code that can be shared across environments.

👉 Example:

```
module "vpc" {
  source     = "./modules/vpc"
  cidr_block = "10.0.0.0/16"
}
```
Modules help you avoid writing the same code again and again.

#### 🔸 Q8. What are Outputs in Terraform?
Answer:
Outputs print useful information after the infrastructure is deployed.

👉 Example:

```
output "public_ip" {
  value = aws_instance.web.public_ip
}
```
This will display the EC2 instance’s IP after deployment.

#### 🔸 Q9. How do you manage Terraform state in a team?
Answer:

Use remote backend (like S3 bucket + DynamoDB)

Enables state locking, conflict prevention, and central storage

Avoids two team members changing the same infra at once

#### 🔸 Q10. How to handle sensitive data in Terraform?
Answer:

Use sensitive = true for variables

Avoid hardcoding secrets

Store secrets in AWS Secrets Manager or Vault

Never commit *.tfstate or terraform.tfvars to Git if they have secrets

#### 🔸 Q11. What is the lifecycle block in Terraform?
Answer:
Used to control how Terraform behaves when a resource changes.

👉 Example:

```
resource "aws_instance" "web" {
  lifecycle {
    prevent_destroy = true
  }
}
```
This stops the EC2 from being accidentally destroyed.

#### 🔸 Q12. How do you implement multi-environment (dev/stage/prod) infra in Terraform?
Answer:

Use workspaces, or Use folder structure:

```
/modules
  /vpc
  /ec2
/dev
  main.tf
  terraform.tfvars
/prod
  main.tf
  terraform.tfvars
```
Each environment can reuse the same module with different inputs.

####  Q13. What is the difference between terraform plan and terraform apply?
Answer:

terraform plan – shows what changes Terraform will make

terraform apply – actually makes the changes in cloud

#### 🔸 Q14. What are some best practices in Terraform?
✅ Use remote backend
✅ Enable state locking
✅ Use modules
✅ Keep code in Git
✅ Don’t hardcode values
✅ Use variables and outputs
✅ Use ```terraform fmt``` and ```terraform validate```
✅ Use ```.terraformignore```

#### 🔸 Q15. How do you handle errors or troubleshoot in Terraform?
Answer:

Read error logs carefully

Use ```terraform plan``` to preview issues

Use ```terraform refresh``` if state is outdated

Check state file for drift

Manually import resources if they exist outside Terr



