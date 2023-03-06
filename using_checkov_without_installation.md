# Using Checkov without Installation

- this is a breif summary of [the pages under this](https://www.checkov.io/4.Integrations/Docker.html)

## Estimated Duration

- 5 minutes, unless you have to play around with the command lines they provide

## Steps to Follow

- create a new batch file with a name like `run-checkov.cmd` and place the following contents in it
  ```bat
  @echo on
  docker run --rm -it -v "%CD%:/src" bridgecrew/checkov --download-external-modules --enable-secret-scan-all-files --quiet --directory /src
  ```
- execute `run-checkov.cmd` to get output like the examples below
  - the above command line is meant as a "sledgehammer", trying to cover as many scenarios as possible
  - can play around with configuration and arguments to fine tune the output

## Notes

- The Checkov command is being executed on a directory mounted into the docker container as `/src`
- Any adjustments to the above script need to take the mount location into account

## Examples

```text
terraform scan results:

Passed checks: 73, Failed checks: 99, Skipped checks: 0

Check: CKV_AWS_79: "Ensure Instance Metadata Service Version 1 is not enabled"
        FAILED for resource: aws_instance.app_a
        File: /learn-terraform-count/main.tf:99-122
        Guide: https://docs.bridgecrew.io/docs/bc_aws_general_31

                99  | resource "aws_instance" "app_a" {
                100 |   depends_on = [module.vpc]
...
dockerfile scan results:

Passed checks: 89, Failed checks: 8, Skipped checks: 0

Check: CKV_DOCKER_7: "Ensure the base image uses a non latest version tag"
        FAILED for resource: /test/dash/dockerfile.FROM
        File: /test/dash/dockerfile:1-1
        Guide: https://docs.bridgecrew.io/docs/ensure-the-base-image-uses-a-non-latest-version-tag

                1 | FROM ubuntu:latest
...
secrets scan results:

Passed checks: 0, Failed checks: 1, Skipped checks: 0

Check: CKV_SECRET_13: "Private Key"
        FAILED for resource: f5fc0aaf45ba947dbfea16e6d40de20500b58117
        File: /test/test:1-2
        Guide: https://docs.bridgecrew.io/docs/git_secrets_13

                1 | -----BEGIN ************************
```