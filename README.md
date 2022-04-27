- [Welcome](#welcome)
  * [Setting vagrant on Fedora host](#setting-vagrant-on-fedora-host)
  * [Running the project](#running-the-project)
  * [Changing the default values for dev-scripts](#changing-the-default-values-for-dev-scripts)

# Welcome

This project creates a virtual machine with all requirements, settings and repos to build [dev-scripts project](https://github.com/openshift-metal3/dev-scripts) (vms).

- Helps isolate your development environment
- Quick build environment via `vagrant up` command (no need to worry about packages, settings etc)
- Easily adjustment requirements via `Vagrantfile`

**Requirements for running the dev-scripts VMs**:

- 70GB of memory
- 100GB of disk space

## Setting vagrant on Fedora host
Installing the basic on Fedora **host** to run the guests:

```
$ sudo dnf install @vagrant
```

Installing the recommended vagrant plugins:

```
$ vagrant plugin install vagrant-libvirt vagrant-ssh
```

Setting the proper permission for qemu:
```
echo "allow all" | sudo tee /etc/qemu/${USER}.conf
echo "include /etc/qemu/${USER}.conf" | sudo tee --append /etc/qemu/bridge.conf
sudo chown root:${USER} /etc/qemu/${USER}.conf
sudo chmod 640 /etc/qemu/${USER}.conf
```

## Running the project

**1.** Clone this git tree:
```
$ git clone https://github.com/dougsland/vagrant-devscripts && cd vagrant-devscripts
```

**2.** Download `pull-secret.json` file in current directory from https://cloud.redhat.com/openshift/install/pull-secret


**3.** Get a login token from [console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com](console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com)
- After logging into [console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com](console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com) go to your name on the top of right
and select `Copy login command`.  

- Copy the string which starts with **sha256**, example: *sha256~jtjonmsjN30REvkhkCEaldsakermcereori* and paste into **config_devel.sh**.

**Example**:
```
# You can get this token from:  
# https://console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com/
# by clicking on your name in the top right corner and coping the login
# command (the token is part of the command)
set +x
export CI_TOKEN='sha256-yuisdfgkjmnwqwert'
set -x
<snip>
```

**4.** Now you are ready to run [dev-scripts](https://github.com/openshift-metal3/dev-scripts) project.
```
$ vagrant up
$ vagrant ssh devscripts-ongo
$ sudo su devel
$ cd /home/devel/dev-scripts
$ make all
```
**NOTE**: devel user password is *vagrant* and [set here](https://github.com/dougsland/vagrant-devscripts/blob/3fd5750389a1e60300d3983f31cdff038822c6a9/Vagrantfile#L12) 

## Changing the default values for dev-scripts
Feel free to edit and change the default values in [vars_devscripts](https://github.com/dougsland/vagrant-devscripts/blob/ca3e7cb29c2e453a340095882281480fe3cd35fe/vars_devscripts#L1) file. It will be added to **.bashrc** in the developer home.
