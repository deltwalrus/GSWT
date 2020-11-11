# Getting Started with Terraform

Terraform is the most popular language for defining and provisioning infrastructure as code. As a demonstration of the purpose and capabilities of Terraform, the following guide will show you how to:

- Set up and configure Terraform
- Use Terraform to create a Docker container running a web server
- Destroy the created resource when no longer needed

## Prerequisites for this guide

- Install [Docker Desktop](https://www.docker.com/products/docker-desktop) for your operating system
- Download the [Terraform binary](https://www.terraform.io/downloads.html) for your operating system and ensure it resides within a directory in your system's `$PATH` *OR* follow the [instructions to install the appropriate Terraform package](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started) for your operating system 

## Configure Terraform

*Note: The example below illustrates Terraform running in a Mac OS X or Linux operating environment.*

For this example, we recommend that you create a new directory on your local machine and create Terraform configuration code inside of it:

```shell
$ mkdir tfdemo
$ cd tfdemo
```

Next, create a file for your Terraform configuration code:

```shell
$ touch main.tf
```

Paste the following configuration into the `main.tf` file to tell Terraform to create a Docker container running the `nginx` web server:

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
  name  = "example"
  ports {
    internal = 80
    external = 80
  }
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

Once your configuration file is ready, initialize your working Terraform directory with the `init` command to prepare it for use:

```shell
$ terraform init
```

You shoud check the output of this command for any errors and follow the provided suggestions to fix them. If it ran successfully, you should see output similar to the below:

```
Initializing the backend...

Initializing provider plugins...
- Finding latest version of terraform-providers/docker...
- Installing terraform-providers/docker v2.7.2...
- Installed terraform-providers/docker v2.7.2 (signed by HashiCorp)

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

## Create infrastructure
You can now provision the resource you specified with the `apply` command:

```shell
$ terraform apply
```

You will be shown a summary (or *plan*) of what actions will be taken based on the contents of your `main.tf` configuration file, and you will be prompted to continue by entering `yes` at the prompt. The command will take a few moments to run and will display a message like the below example indicating that the resource was created:

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + dns              = (known after apply)
      + dns_opts         = (known after apply)
      + entrypoint       = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + log_opts         = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + restart          = "no"
      + rm               = false
      + shm_size         = (known after apply)
      + start            = true
      + user             = (known after apply)
      + working_dir      = (known after apply)

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id     = (known after apply)
      + latest = (known after apply)
      + name   = "nginx:latest"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Still creating... [10s elapsed]
docker_image.nginx: Creation complete after 14s [id=sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=3c8100d3334d58f55f0c76ba1dbfb4d5a43f93ad010b529b485d91f051a7afaf]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

## Destroy infrastructure

Finally, you can destroy infrastructure using the `destroy` command:

```shell
$ terraform destroy
```

Similar to the `apply` command, you will be shown a summary of planned actions and you will be prompted for confirmation. Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier, with output such as the following:

```shell
docker_image.nginx: Refreshing state... [id=sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest]
docker_container.nginx: Refreshing state... [id=3c8100d3334d58f55f0c76ba1dbfb4d5a43f93ad010b529b485d91f051a7afaf]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "3c8100d3334d" -> null
      - id                = "3c8100d3334d58f55f0c76ba1dbfb4d5a43f93ad010b529b485d91f051a7afaf" -> null
      - image             = "sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1" -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway          = "172.17.0.1"
              - ip_address       = "172.17.0.2"
              - ip_prefix_length = 16
              - network_name     = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id     = "sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest" -> null
      - latest = "sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1" -> null
      - name   = "nginx:latest" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=3c8100d3334d58f55f0c76ba1dbfb4d5a43f93ad010b529b485d91f051a7afaf]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

## Next steps

This guide has demonstrated how to set up and use Terraform to create and destroy infrastructure on your local system.  Now that you have seen how Terraform uses infrastructure as code to manage a system's lifecycle, we recommend the following resources to help you learn more about how to extend Terraform's functionality into the cloud and beyond:

- Our learning portal provides many [Terraform tutorials](https://learn.hashicorp.com/terraform) tailored to specific use-cases
- The [Terraform documentation](https://www.terraform.io/docs/index.html) page offers a deeper dive into Terraform's features
- You can find the latest news about all of our products at the [Hashicorp blog](https://www.hashicorp.com/blog)
