# -*- mode: ruby -*-
# vi: set ft=ruby :
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'libvirt'

######################## GLOBAL VARIABLES ###########
VAGRANT_COMMAND = ARGV[0]

DEVELOPER_NAME = "Douglas Schilling Landgraf"
DEVELOPER_EMAIL = "dougsland@redhat.com"

DEVELOPER_USERNAME = "devel"
DEVELOPER_PASSWORD = "superpass"
REQUIRED_PACKAGES = "vim git make wget jq podman sudo python3-pip" 

VM_NAME = "devscripts-ongo"
VM_HOSTNAME = "devscripts-ongo"
VM_IMAGE = "centos/stream8"

EXTRA_DISK = "/dev/vdb"
EXTRA_DISK_PARTITION = "/dev/vdb1"
EXTRA_DISK_PATH="/source"

# "fedora/35-cloud-base"
# "generic/rhel8"
#"centos/stream8"

VM_CPUS = 5
VM_CPU_MODE = "host-passthrough"
VM_MEMORY = 95048
VM_NESTED = true
VM_KEYMAP = "pt"
VM_DISKSIZE = "100GB"

VM_SYNC_FOLDER = "/vagrant"

GO_VERSION = "1.17.1"
GO_PACKAGE = "go" + GO_VERSION + ".linux-amd64.tar.gz"
GO_SRC_DIR = '/home/' + DEVELOPER_USERNAME + '/go/src/github.com/openshift/'

CMD_CREATE_GO_DIRS = 'sudo -u ' + DEVELOPER_USERNAME + ' mkdir -p ' + GO_SRC_DIR
CMD_GIT_CONFIG_GLOBAL_USERNAME = "sudo -u " + DEVELOPER_USERNAME +
	" git config --global user.name \"" + DEVELOPER_NAME + "\""

CMD_GIT_CONFIG_GLOBAL_USEREMAIL = "sudo -u " + DEVELOPER_USERNAME +
	" git config --global user.email \"" + "" + DEVELOPER_EMAIL + "\""

######################## GLOBAL VARIABLES ###########

######################## START ######################
Vagrant.configure(2) do |config|
  config.vm.box = VM_IMAGE
  config.disksize.disk = VM_DISKSIZE

  config.vm.provider 'libvirt' do |lv, config|
    lv.storage :file, :size => '110G', :type => 'qcow2'
    lv.memory = VM_MEMORY
    lv.cpus = VM_CPUS
    lv.cpu_mode = VM_CPU_MODE
    lv.nested = VM_NESTED
    lv.keymap = VM_KEYMAP
    config.vm.synced_folder '.', VM_SYNC_FOLDER, type: 'nfs', nfs_udp: false,
				 :linux__nfs_options => ['rw','no_subtree_check','no_root_squash']
  end

  # Set hostname and vm name
  config.vm.define VM_NAME
  config.vm.hostname = VM_HOSTNAME

  # update the system 
  #config.vm.provision 'shell',
  #		inline: 'sudo dnf update -y'

  # Add new user
  config.vm.provision 'shell',
		inline: 'useradd ' + DEVELOPER_USERNAME 

  config.vm.provision 'shell',
		inline: 'usermod -a -G nobody ' + DEVELOPER_USERNAME

  config.vm.provision 'shell',
		inline: 'echo ' + DEVELOPER_PASSWORD + ' | passwd ' + DEVELOPER_USERNAME + ' --stdin'

  # sudoers
  config.vm.provision 'shell',
		inline: 'echo ' + DEVELOPER_USERNAME + ' ALL=NOPASSWD:ALL > /etc/sudoers.d/' + DEVELOPER_USERNAME

  config.vm.provision 'shell',
		inline: 'chmod 0440 /etc/sudoers.d/' + DEVELOPER_USERNAME

  config.vm.provision 'shell',
		inline: 'gpasswd wheel -a ' + DEVELOPER_USERNAME

  # Installing Fedora packager tools
  config.vm.provision 'shell',
		inline: 'sudo dnf install -y ' + REQUIRED_PACKAGES

  # Installing GO
  config.vm.provision 'shell',
		inline: 'wget https://dl.google.com/go/' + GO_PACKAGE

  config.vm.provision 'shell',
		inline: 'sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf ' + GO_PACKAGE
  
  config.vm.provision 'shell',
		inline: 'rm -f ${version}.linux-amd64.tar.gz'

  config.vm.provision 'shell',
		inline: 'echo export PATH=$PATH:/usr/local/go/bin >> ' + ' /home/' + DEVELOPER_USERNAME + '/.bashrc'

  # Clonning repos
  config.vm.provision 'shell',
		inline: CMD_CREATE_GO_DIRS

  config.vm.provision 'shell',
		inline: 'sudo -u ' + DEVELOPER_USERNAME +
			' git clone https://github.com/openshift/baremetal-runtimecfg ' + GO_SRC_DIR + '/baremetal-runtimecfg'

  config.vm.provision 'shell',
		inline: 'sudo -u ' + DEVELOPER_USERNAME +
			' git clone https://github.com/openshift/machine-config-operator ' + GO_SRC_DIR + '/machine-config-operator'

  ############################ START - Creating the extra partition for devscripts
  config.vm.provision 'shell',
		inline: 'echo -e "o\nn\np\n1\n\n\nw" | fdisk ' + EXTRA_DISK

  config.vm.provision 'shell',
		inline: 'echo -e "y\n" | mkfs.ext4 ' + EXTRA_DISK_PARTITION

  config.vm.provision 'shell',
		inline: 'mkdir -p ' + EXTRA_DISK_PATH

  config.vm.provision 'shell',
		inline: 'mount -t ext4 ' + EXTRA_DISK_PARTITION + ' ' + EXTRA_DISK_PATH

  config.vm.provision 'shell',
		inline: 'chown -R ' + DEVELOPER_USERNAME + ':' + DEVELOPER_USERNAME + ' ' + EXTRA_DISK_PATH

  config.vm.provision 'shell',
		inline: 'chmod -R 0775 ' + EXTRA_DISK_PATH
  ############################ END - Creating the extra partition for devscripts

  config.vm.provision 'shell',
		inline: 'sudo -u ' + DEVELOPER_USERNAME +
			' git clone https://github.com/openshift-metal3/dev-scripts ' + EXTRA_DISK_PATH + '/dev-scripts'


  # Deploying templates
  config.vm.provision "file", source: "config_devel.sh", destination: "/tmp/config_devel.sh"
  config.vm.provision "file", source: "vars_devscripts", destination: "/tmp/vars_devscripts"
  config.vm.provision "file", source: "pull_secret.json", destination: "/tmp/pull_secret.json"

  # vm.provision shell has privileged permission
  config.vm.provision "shell",
      inline: "mv /tmp/config_devel.sh " + EXTRA_DISK_PATH + '/dev-scripts/config_devel.sh'

  config.vm.provision "shell",
      inline: "mv /tmp/vars_devscripts " + EXTRA_DISK_PATH + '/dev-scripts/vars_devscripts'

  config.vm.provision "shell",
      inline: "mv /tmp/pull_secret.json " + EXTRA_DISK_PATH + '/dev-scripts/pull_secret.json'

  config.vm.provision 'shell',
		inline: 'sudo -u ' + DEVELOPER_USERNAME +
			' cat ' + EXTRA_DISK_PATH + '/dev-scripts/vars_devscripts >> /home/' + DEVELOPER_USERNAME + '/.bashrc'
  # Deploying templates



  ############################ START - Symbol link projects to /home
  config.vm.provision 'shell',
		inline: 'sudo -u ' + DEVELOPER_USERNAME +
			' ln -s ' + GO_SRC_DIR + '/machine-config-operator /home/' + DEVELOPER_USERNAME + "/machine-config-operator"

  config.vm.provision 'shell',
		inline: 'sudo -u ' + DEVELOPER_USERNAME +
			' ln -s ' + GO_SRC_DIR + '/baremetal-runtimecfg /home/' + DEVELOPER_USERNAME + "/baremetal-runtimecfg"

  config.vm.provision 'shell',
		inline: 'sudo -u ' + DEVELOPER_USERNAME +
			' ln -s ' + EXTRA_DISK_PATH + '/dev-scripts /home/' + DEVELOPER_USERNAME + "/dev-scripts"
  ############################ END - Symbol link projects to /home

  # Setting git configs
  config.vm.provision 'shell', inline: CMD_GIT_CONFIG_GLOBAL_USERNAME
  config.vm.provision 'shell', inline: CMD_GIT_CONFIG_GLOBAL_USEREMAIL

  config.vm.provision 'shell', inline: 'echo ' + '==============================================================='
  config.vm.provision 'shell', inline: 'echo ' + 'ALL SET!'
  config.vm.provision 'shell', inline: 'echo ' + '- To login in the VM use: vagrant ssh ' + VM_NAME
  config.vm.provision 'shell', inline: 'echo ' + '- fedpkg clone PACKAGE_NAME for cloning a package'
  config.vm.provision 'shell', inline: 'echo ' + '==============================================================='
end
