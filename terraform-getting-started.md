# Getting Started with Terraform

Terraform is the most popular language for defining and provisioning infrastructure as code (IaC).

## Prerequisites for this example

- Install [Docker Desktop](https://www.docker.com/products/docker-desktop) for your operating system
- Download the [Terraform](https://www.terraform.io/downloads.html) binary for your operating system and ensure it resides within a directory in your system's `$PATH`

## Preparing to run Terraform
Once Terraform is installed, you're only a few steps away from creating infrastructure.

Most users find it easiest to create a new directory on their local machine and create Terraform configuration code inside of it.

```shell
$ mkdir tfdemo
$ cd tfdemo
```

Next, create a file for your Terraform configuration code.

```shell
$ touch main.tf
```

Paste the following configuration into the file:

```hcl
terraform {
  required_providers {
    docker = {
      source = "terraform-providers/docker"
    }
  }
}

provider "docker" {}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

Initialize Terraform with the `init` command. The Docker provider will be automatically installed. 

```shell
$ terraform init
```

You shoud check the output of this command for any errors and follow the provided suggestions to fix them. If it ran successfully, you should see output similar to the below:

```shell
Initializing the backend...

Initializing provider plugins...
- Using previously-installed terraform-providers/docker v2.7.2

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, we recommend adding version constraints in a required_providers block
in your configuration, with the constraint strings suggested below.

* terraform-providers/docker: version = "~> 2.7.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Provision the resource with the `apply` command.

```shell
$ terraform apply
```

You will be shown a summary of what the command will do and will be prompted to continue by entering `yes` at the prompt. The command will take a few moments to run and will display a message indicating that the resource was created.

Finally, destroy the infrastructure.

```shell
$ terraform destroy
```

Similar to the `apply` command, you will be shown a summary of planned actions and you will be prompted for confirmation. Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier.
