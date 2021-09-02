# k3d-dev-up

Deploy k3d dev environment to AWS, including:

- creating an ec2 security group
- creating a private key
- creating a local .pem file
- creating an ec2 key pair
- creating an ec2 instance
- creating a k3d cluster on the instance
- installing Big Bang's flux on the k3d cluster
- copying the cluster's kubeconfig to your local machine

## Prerequisites

- [AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- [Terraform](https://www.terraform.io/downloads.html)
- [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)

> Note for WSL Users: Run `Terraform` from the native WSL file system and not a `/mnt` mapped to a NTFS file system. The CHMOD on the PEM file will fail.

Shell commands used:

- `scp` - to copy the remote kubeconfig to local machine
- `sed` - to update the kubeconfig's server IP

## Instructions

1. Check for an existing AWS profile

```shell
aws configure list-profiles
```

1. If desired profile is not present, configure a profile

```shell
aws configure --profile <PROFILE_NAME>
# aws_access_key_id - The AWS access key part of your credentials
# aws_secret_access_key - The AWS secret access key part of your credentials
# region - us-gov-west-1
# output - json
```

1. Set variables in variables.yaml

```yaml
# example
awsProfile: default
awsUsername: first.last
instanceType: t2.2xlarge
volumeSize: 50
k3dVersion: v4.4.7
registryUsername: first.last
registryPassword: abcd1234
```

1. Initialize terraform (first time only)

```shell
terraform init
```

1. Create infrastructure (kubeconfig is dumped into working directory as `k3d.yaml`)

```shell
terraform apply
```

1. Test cluster access (you should see some pods in the `kube-system` and `flux-system` namespaces)

```shell
kubectl --kubeconfig=./k3d.yaml get pods --all-namespaces
```

## Cleanup

```shell
terraform destroy
```

## Contributing

Please open an issue or PR if you'd like to see something changed.
