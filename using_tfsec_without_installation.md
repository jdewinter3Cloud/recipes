# Using TfSec without Installation

- this is a breif summary of [the pages under this](https://aquasecurity.github.io/tfsec/)

## Estimated Duration

- 5 minutes, unless you have to play around with the command lines they provide

## Steps to Follow

- create a new batch file with a name like `run-tfsec.cmd` and place the following contents in it
  ```bat
  @echo off
  docker run --rm -it -v "%CD%:/src" aquasec/tfsec --concise-output --force-all-dirs --format json /src
  ```
- execute `run-tfsec.cmd` to get output like the examples below
  - the above command line is meant as a "sledgehammer", trying to cover as many scenarios as possible
  - can play around with configuration and arguments to fine tune the output

## Notes

- The TfSec command is being executed on a directory mounted into the docker container as `/src`
- Any adjustments to the above script need to take the mount location into account

## Examples

```json
{
        "results": []
}
```

```json
{
    "results": [
        {
            "rule_id": "AVD-AWS-0131",
            "long_id": "aws-ec2-enable-at-rest-encryption",
            "rule_description": "Instance with unencrypted block device.",
            "rule_provider": "aws",
            "rule_service": "ec2",
            "impact": "The block device could be compromised and read from",
            "resolution": "Turn on encryption for all block devices",
            "links": [
                "https://aquasecurity.github.io/tfsec/v1.28.1/checks/aws/ec2/enable-at-rest-encryption/",
                "https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#ebs-ephemeral-and-root-block-devices"
            ],
            "description": "Root block device is not encrypted.",
            "severity": "HIGH",
            "warning": false,
            "status": 0,
            "resource": "aws_instance.app_a",
            "location": {
                "filename": "/src/main.tf",
                "start_line": 99,
                "end_line": 122
            }
        },
        ...
    ]
}
```