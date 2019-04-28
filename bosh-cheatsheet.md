# BOSH command line interface cheatsheet

cheatsheet (https://cheatsheet.dennyzhang.com/cheatsheet-bosh-a4)


### Introduction

BOSH is a powerful tool to install and manage your deployments. You can find docs on https://bosh.io/docs. Consider using `bosh-init` tool to install to a cloud of your choice MicroBOSH instance (MicroBOSH is a single VM BOSH installation, it has everything that you need to deploy and manage).

### Installing of BOSH CLI

BOSH command line interface is implemented as a ruby gem and can be run on every platform that supports ruby, you'll need to have ruby 2.1.x or higher to run it. To install it you can run `gem install bosh_cli` and `gem update bosh_cli` to update to a newer version.

### Bootstrap the cloud with BOSH


To start to work with BOSH on your cloud you’ll need to run MicroBOSH VM on your IaaS. MicroBOSH (also it is referred as BOSH director) is a single VM with all needed BOSH components to manipulate your cloud. Since MicroBOSH is run on one VM it is not meant to be highly available, so don’t consider to use it in production environments. To run MicroBOSH on your cloud you need to use a tool that is called `bosh-init`, you can find details here [https://bosh.io/docs/install-bosh-init.html].

### Where to start?

After you have access BOSH installation, you'll need to login to it and find out what clusters does it manage. This paragraph will help you to do it, also it will get you acquainted with basic BOSH terminology. You are highly incouraged to visit page with BOSH docs: [https://bosh.io/docs]

`bosh help` - shows you well formated help message on all commands BOSH CLI has. if you need to help on a specific command, run `bosh <command> -h`. 

** `bosh target <bosh-director-host>` - sets your BOSH CLI to work with specific BOSH installation.

** `bosh login` - login into BOSH director, it will store your target with creds to `~/.bosh_config` by default. if you need to run this command interactively, pass creds as its parameters: `bosh login <username> <password>`

** `bosh status` - shows all actual options of BOSH director and current deployment.

`bosh deployments` - shows deployments list you have on your target bosh-director.

`bosh deployment` - shows the file that has current deployment description.

** `bosh deployment /path/to/manifest.yml` - set the file to contain current deployment description. this file is referred as manifest often.

** `bosh vms` - shows all virtual machines that were deployed by the BOSH.

`bosh stemcells` - see the list of all stemcells you have. [Stemcell](https://bosh.io/docs/terminology.html#stemcell) is an image of OS that BOSH uses to create your VMs.

`bosh releases` - see the list of all releases. [release](https://bosh.io/docs/terminology.html#compiled-release) is an archive with all sources and scripts to configure and run any service with BOSH.

`bosh errands` - see the list of errand jobs you have. [errands](https://bosh.io/docs/terminology.html#errand) are ..

`bosh locks` - every time BOSH updates deployments it puts locks on them, this command allows to see list of them.

`bosh tasks` - see every task/event that occurred in BOSH deployment.

`bosh cloud-config` - see the setup of your cloud. [cloud-config](https://bosh.io/docs/terminology.html#cloud-config) is settings to connect to manage your cloud, it has configuration to connect, networks, flavors and other resources.

`bosh download manifest <deployment-name> /path/to/manifest.yml` - downloads configuration of the deployment. as a file. it's useful

### Manage deployment

BOSH is a great tool to run your clusters ... This paragraph shows how to run your deployment with BOSH.

** `bosh update cloud-config cloud.yml` - upload settings of your cloud to BOSH director from `cloud.yml` file.

** `bosh deployment <deployment-file>` - set deployment manifest.

`bosh edit deployment` - opens manifest of the current deployment in text editor.

`bosh upload stemcell <stemcell-path-or-url>` - upload stemcell to BOSH director; you can pass this command a path to a file with stemcell or URL, where this stemcell can be fetched.

`bosh upload release <release-path-or-url>` - upload release to BOSH director; you can pass this command a path to a file with release or URL, where this stemcell can be fetched.

** `bosh -n deploy` - deploy changes to the current cluster; `-n` means interactive mode.

** `bosh ssh <job-name>/<instance-index>` - ssh to job instances.

`bosh logs <job-name> <job-index>` - fetch tarball with logs for specific BOSH job.

`bosh public stemcells --all` - see the list of available publically stemcells (you can also find them on [https://bosh.io/stemcells]).

`bosh download public stemcell <stemcell-filename>` - download stemcells available publically stemcell on your computer

`bosh run errand <errand-name>` - run specified errand.

`bosh task <task-number>` - display task status and start tracking its output.

`bosh cancel task <task-number>` - stop task once it reaches the next checkpoint.

### Maintenance and Troubleshooting

BOSH not only deploy your cluster, but also provides lifecycle support. This paragraph shows main commands that help you to maintain and support your cluster.

`bosh cloudcheck <deployment-name>` - very useful command if something went wrong, forces BOSH to compare desired state of your infrastructure and the one you actually have. After BOSH finds mismatch, it will try to recover this error providing you with possibility to choose action to recover.

`bosh instances --ps` - when cloudcheck is of no help, this gives a detailed status of all jobs in the current deployment. A very important hint!

`bosh -n stop --hard <job-name>/<job-index>` - poweroff a given instance so that it doesn't get in the way of recreating another one in the same cluster (very common with the Diego etcd cluster: recreating `database_z3/0` requires `database_z1/0` to be poweroff first)

`bosh restart <job-name> <job-index>` - restart VM with BOSH job.

`bosh recreate <job-name> <job-index>` - recreates VM with BOSH job.

`bosh backup` - creates a backup of BOSH director database, this allows you to recover your BOSH very quickly if it fails.

`bosh cleanup` - removes all unused releases and stemcells.

#### Toubleshooting BOSH instances

`bosh ssh <job-name>/<job-index>` - Connects to a BOSH instance.

`sudo -i` - Mandatory when connecting to a BOSH instance! Otherwise you won't get `monit` in your `$PATH`!

`watch "monit summary && echo && tail /var/vcap/monit/monit.log && echo && find /var/vcap/data/sys/log -type f -ls"` - Watch for monit status, latest monit activity, and log files activity. Useful when you try to spot an issue without knowing exactly which one in advance.

`[ -z "$(command -v jq)" ] && apt-get -y install jq; jq -r '"\(.job.name)/\(.index)"' /var/vcap/bosh/spec.json` - To know exactly on which instance you are connecte. Very useful when you need to open dozen os ssh connections!

`find /var/vcap/data/sys/log -ls` - Lists all log files for all jobs, so that you can select one to look at.

`tail -F $(find /var/vcap/monit/monit.log /var/vcap/data/sys/log -type f)` - Follow all log files on the instance. Also know as the “mega tail”.

#### Toubleshooting BOSH instances of a failing consul cluster

`/var/vcap/packages/consul/bin/consul monitor -log-level=debug` - Follow consul logs at `debug` level, even when the actual log file is at a higher `info` or `warn` level, which is common case. This is supra helpful to troubleshoot the Cloud Foundry consul cluster!

`tail -F /var/vcap/data/sys/log/consul_agent/consul_agent.stdout.log /var/vcap/monit/monit.log` - Another variant that focuses on interactions between consul, its _undocumented_ “confab” wrapper, and monit. Very common use case in a failing consul cluster.

`/var/vcap/packages/consul/bin/consul members | tail -n +2 | sort` - Lists the consul member in a consistent way, to properly check for split-brain situations.

### Creating releases

To use BOSH you'll need to create releases, BOSH commpunity has plenty of ready to use releases that you can find here (https://bosh.io/releases). But if consider to create your own BOSH release we advise you to use `bosh-gen` gem and following commands:

** `bosh create release --with-tarball` - run this command in the folder with release to get release tarball, this will create developer release.

`bosh add blob <local_path> [<blob_dir>]` - add a blob (binary file or source code) to the release you create.

`bosh blobs` - list of blobs that are already added to the release.

### Disks

Disks are vital in BOSH deployments to store and share state between jobs.

`bosh disks --orphaned` - see all disks that are not attached to VMs.

`bosh attach disk <job-name> <index> <disk-id>` - attach disk to job VM.

After deploying any new release, you should check that all necessary persistent disks have been created. Otherwise if anything went wrong with the spiff templating (quite common), you'll lose data anytime your deployment goes bad.

`bosh instances --details` - displays attached disks for all instances of the current deployment. (Attached disks are mounted to `/var/vcap/store` and when connected to an instance, they are visible with `mount`.)
