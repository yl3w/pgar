# Pulumi Github Action Runner
Pulumi IaC for running Github Runners on AWS.

## Getting Started

### Log into Pulumi service
```
pulumi logic
```

### Select the organization and stack
```
pulumi stack select accrue-money/p-gar/ci-cd
```

_Make sure you are invited to the organization_

### To set up Github Action runners 

```
pulumi up
```

### To destroy Github action runners

```
pulumi destroy
```

## Implementation

* Creates a VPC in 2 AZs
* Each AZ has a private and public subnet
* Each private subnet is configured with an instance of Github Runner
* Each public subnet is configured with a host for use with SSH
* NAT gateway is created in private subnet to route traffic from `Github Runner`
* Internet gateway is created in public subnet to route traffic to the internet
* An autoscaling group is utilized to ensure that there are at least 2 runners at any time.
* A lifecycle hook is associated with the instance in the auto scaling group
* A lambda is invoked to deregister / remove the runner when the EC2 instance in the auto scaling group is terminated (**IN-PROGRESS**)
* User Data script is utilized to register EC2 instance as a runner.


# Configurations

Configure pulumi with values for `aws:region`, `GITHUB_ACCESS_TOKEN`, `GITHUB_ACTIONS_RUNNER_CONTEXT` `keyName`.

`GITHUB_ACCESS_TOKEN` is PAT token save it to config as a secret

```
pulumi config set --secret GITHUB_ACCESS_TOKEN XXXX
```

`GITHUB_ACTIONS_RUNNER_CONTEXT` can be specified in two formats. One for user/repository for instance `https://github.com/maddalab/pulumi-poetry-actions/` or alternatively for organizations for instance `https://github.com/accrue-money`

Github runners for a single repository

```
pulumi config set GITHUB_ACTIONS_RUNNER_CONTEXT https://github.com/maddalab/pulumi-poetry-actions/
```

Github Runners for entire organization

```
pulumi config set GITHUB_ACTIONS_RUNNER_CONTEXT https://github.com/accrue-money
```

# TODO

* Currently this utilized a PAT (_personal account token associated with @maddalab's account_) Replace it with a Organizational app token.
* Complete lambda implementation to handle lifecycle of EC2 instance,

# How to use

The intent of this repository is to both develop a pulumi solution for `Github Runner` and to dog food it for development as CI/CD. However by default the runners are shut down and not in use. To utilize the runners, follow the steps as below

```
# if you have multiple AWS profiles in your `credentials` file.
export AWS_PROFILE=<>
pulumi login
pulumi up
```
