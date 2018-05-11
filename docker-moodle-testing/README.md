# Moodle Docker environment for test runs

This template allows you to run Behat and PHPUnit test runs against a Moodle codebase using Docker containers.
To run tests, follow the instructions in the [nightly scripts repository](https://git.in.moodle.com/integration/nightlyscripts/blob/master/runner/README.md).

## Example usage
```
$ # Create resource group
$ az group create --name=moodletest --region=westeurope
$ # Deploy template to resource group
$ key=$(cat ~/.ssh/id_rsa.pub)
$ az group deployment create --resource-group moodletest --template-file docker-moodle-testing/azuredeploy.json --parameters dnsNameForPublicIP=moodledocker1 --parameters "sshKeyData=$key" --parameters newStorageAccountName=moodledocker1
$ # Run some tests
$ ssh -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no moodle@moodledocker1.westeurope.cloudapp.azure.com "export BUILD_ID=1 && export WORKSPACE=/home/moodle/workspace && export TESTTORUN=behat && export BEHAT_TOTAL_RUNS=4 && export TAGS=mod_forum,mod_assign && cd nightlyscripts && ./runner/master/run.sh"
$ # Download the results
$ scp -R -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no moodle@moodledocker1.westeurope.cloudapp.azure.com:~/workspace/1 ./results
$ # De-provision resource group
$ az group delete --name=moodletest
```

## Available parameters
* newStorageAccountName: Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed.
* dnsNameForPublicIP: Unique DNS Name for the Public IP used to access the Virtual Machine.
* sshKeyData: SSH rsa public key file as a string.
* adminUsername: Username for the Virtual Machine (default: moodle)
* vmSize: VM size for the Docker host. (default: Standard_F1)
* ubuntuOSVersion: The Ubuntu version for deploying the Docker containers. This will pick a fully patched image of this given Ubuntu version. Allowed values: 14.04.4-LTS, 15.10, 16.04.0-LTS (default: 16.04.0-LTS)
* moodleRepository: Git repository URL to clone for moodle code (default: https://github.com/moodle/moodle.git)
* moodleBranch: Git branch to checkout for moodle code (default: master)
* nightlyscriptsRepository: Git repository URL to clone for the nightly test scripts (default: https://git.in.moodle.com/integration/nightlyscripts.git)
* nightlyscriptsBranch: Git branch to checkout for nightly test scripts (default: master)
