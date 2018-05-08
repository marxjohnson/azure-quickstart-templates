# Moodle Docker environment for test runs

This template allows you to run Behat and PHPUnit test runs against a Moodle codebase using Docker containers.
To run tests, follow the instructions in the [nightly scripts repository](https://git.in.moodle.com/integration/nightlyscripts/blob/master/runner/README.md).

## Example usage:
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
