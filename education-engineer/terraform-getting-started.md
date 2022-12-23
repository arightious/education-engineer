# Getting Started with Terraform

Terraform is the most popular language for defining and provisioning infrastructure as code (IaC).

This guide will walk you through installing Terraform and provisioning a NGINX server in a Docker container.

## Prerequisites

- The latest version of Terraform.
- [Docker Desktop](https://docs.docker.com/desktop/) installed and running.

## Install Terraform

To begin using Terraform, you must first install it.

Visit [Terraform.io](https://developer.hashicorp.com/terraform/downloads) and choose the appropriate installation method for your system.

Verify Terraform is properly installed.

```shell
$ terraform
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.
The primary workflow commands are given first, followed by
less common or more advanced commands.

Main commands:
  init          Prepare your working directory for other commands
  validate      Check whether the configuration is valid
  plan          Show changes required by the current configuration
  apply         Create or update infrastructure
  destroy       Destroy previously-created infrastructure

All other commands:
  console       Try Terraform expressions at an interactive command prompt
  fmt           Reformat your configuration in the standard style
  force-unlock  Release a stuck lock on the current workspace
  get           Install or upgrade remote Terraform modules
  graph         Generate a Graphviz graph of the steps in an operation
  import        Associate existing infrastructure with a Terraform resource
  login         Obtain and save credentials for a remote host
  logout        Remove locally-stored credentials for a remote host
  output        Show output values from your root module
  providers     Show the providers required for this configuration
  refresh       Update the state to match remote systems
  show          Show the current state or a saved plan
  state         Advanced state management
  taint         Mark a resource instance as not fully functional
  test          Experimental support for module integration testing
  untaint       Remove the 'tainted' state from a resource instance
  version       Show the current Terraform version
  workspace     Workspace management

Global options (use these before the subcommand, if any):
  -chdir=DIR    Switch to a different working directory before executing the
                given subcommand.
  -help         Show this help output, or the help for a specified subcommand.
  -version      An alias for the "version" subcommand.
```

**Errors:**

If you get the error `command not found`, check your `PATH` variable, and ensure it includes the directory where Terraform was installed.

## Write Terraform Configuration Code

With Terraform installed, you are ready to begin creating infrastructure. In this section, you will use Terraform's configuration language to provision infrastructure.

Create a new directory for your Terraform configuration code.

```shell
$ mkdir terraform-demo
```

Change in to the directory.

```shell
$ cd terraform-demo
```

Create a `main.tf` file.

```shell
$ touch main.tf
```

Using your preferred text editor, paste the following lines into the `main.tf` file. Save your changes.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
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

Before Terraform can run the configuration, the working directory must be initialized. The `init` command will configure the backend, install providers and modules, and create a lock file.

Initialize the configuration.

```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v2.23.1...
- Installed kreuzwerker/docker v2.23.1 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Terraform has configured the backend and installed the `kreuzwerker/docker` provider.

Initialization will also create a lock file `.terraform.lock.hcl` and a `.terrform` directory in your `terraform-demo` directory.

Check the output for errors.

If initialization was successful, preview the actions Terraform will take with the `plan` command.

```shell
$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach                                      = false
      + bridge                                      = (known after apply)
      + command                                     = (known after apply)
      + container_logs                              = (known after apply)
      + container_read_refresh_timeout_milliseconds = 15000
      + entrypoint                                  = (known after apply)
      + env                                         = (known after apply)
      + exit_code                                   = (known after apply)
      + gateway                                     = (known after apply)
      + hostname                                    = (known after apply)
      + id                                          = (known after apply)
      + image                                       = (known after apply)
      + init                                        = (known after apply)
      + ip_address                                  = (known after apply)
      + ip_prefix_length                            = (known after apply)
      + ipc_mode                                    = (known after apply)
      + log_driver                                  = (known after apply)
      + logs                                        = false
      + must_run                                    = true
      + name                                        = "training"
      + network_data                                = (known after apply)
      + read_only                                   = false
      + remove_volumes                              = true
      + restart                                     = "no"
      + rm                                          = false
      + runtime                                     = (known after apply)
      + security_opts                               = (known after apply)
      + shm_size                                    = (known after apply)
      + start                                       = true
      + stdin_open                                  = false
      + stop_signal                                 = (known after apply)
      + stop_timeout                                = (known after apply)
      + tty                                         = false
      + wait                                        = false
      + wait_timeout                                = 60

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + image_id    = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```

Terraform indicates it will add two new resources, `docker_container.nginx` (as declared in line 11 of `main.tf`) and `docker_image.nginx` (as declared on line 19 of `main.tf`).

Execute the configuration.

```shell
$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      ...
      ...
      ...
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      ...
      ...
      ...
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```
(Note: The actual output has been truncated.)

The `apply` command will output the actions Terraform will take, and a prompt to approve the actions.

Type `yes`.

The command may take a few minutes to run and will display a message indicating the resources are created.

```shell
docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 7s [id=sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 0s [id=7540c2d7031b8f401c15973b9eb1f9c36b80ee1aae83c3b4300b56a04307ed0a]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Additionally, a `terraform.tfstate` file will be created.

**Possible Error**:

```shell
Error: Error pinging Docker server: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

This error means Docker is not running on your machine. Start Docker, and run the `apply` command again.

Confirm the container was created.

```shell
$ docker ps -f name=training
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                NAMES
1450b78860c1   1403e55ab369   "/docker-entrypoint.…"   3 seconds ago   Up 2 seconds   0.0.0.0:80->80/tcp   training
```


## Clean up

Finally, destroy the infrastructure created in this guide.

The `destroy` command outputs details listing the resources that will be destroyed.

```shell
$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52nginx:latest]
docker_container.nginx: Refreshing state... [id=7540c2d7031b8f401c15973b9eb1f9c36b80ee1aae83c3b4300b56a04307ed0a]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      ...
      ...
      ...
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      ...
      ...
      ...
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:
```
(Note: The actual output has been truncated.)

Confirm by typing `yes`.

Terraform will output details about the infrastructure it just destroyed.

```shell
docker_container.nginx: Destroying... [id=7540c2d7031b8f401c15973b9eb1f9c36b80ee1aae83c3b4300b56a04307ed0a]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

Confirm the container no longer exists.

```shell
$ docker ps -f name=training
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## Next Steps

In this guide you installed Terraform, created a configuration, and provisioned infrastructure via the Terraform CLI. Continue learning the basics with the [Get Started Tutorials](https://developer.hashicorp.com/terraform/tutorials).

Learn more about the commands and concepts in this guide:

[Terraform Language](https://developer.hashicorp.com/terraform/language)

[Terraform Providers](https://developer.hashicorp.com/terraform/language/providers)

[Terraform CLI Commands](https://developer.hashicorp.com/terraform/cli/commands)

[Lock file](https://developer.hashicorp.com/terraform/language/files/dependency-lock)

[Working Directory Contents](https://developer.hashicorp.com/terraform/cli/init#working-directory-contents)