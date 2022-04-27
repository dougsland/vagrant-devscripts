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

Finally, clone the repo and run `vagrant up`

```
$ git clone https://github.com/dougsland/vagrant-devscripts && cd vagrant-devscripts
$ vagrant up
```
