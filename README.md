# Welcome



## Setting vagrant on Fedora
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

Example:
```
# You can get this token from https://console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com/ by
# clicking on your name in the top right corner and coping the login
# command (the token is part of the command)
set +x
export CI_TOKEN='sha256-yuisdfgkjmnwqwert'
set -x
<snip>
```

**4.** Now you are ready to run [dev-scripts](https://github.com/openshift-metal3/dev-scripts) project.
```
$ vagrant up
```
