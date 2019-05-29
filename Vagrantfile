# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "debian/stretch64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = 2048
     # Add more cores
     vb.cpus = 2
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
     # Install things
     apt-get update
     apt-get -y dist-upgrade
     apt-get -y install build-essential git curl vim-nox tmux python3-pip info
     curl -fsSL https://dl.google.com/go/go1.11.linux-amd64.tar.gz | tar -xzC /usr/local
     echo 'export GOPATH="$HOME/go"' >> /etc/profile
     echo 'PATH="$PATH:/usr/local/go/bin:$GOPATH/bin:$HOME/.local/bin"' >> /etc/profile
     apt-get -y install apt-transport-https ca-certificates curl gnupg2 software-properties-common
     curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
     add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian stretch stable"
     apt-get update
     apt-get install -y docker-ce=18.06.1~ce~3-0~debian
     curl -fsSLo /usr/local/bin/minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x /usr/local/bin/minikube
     curl -fsSLo /usr/local/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x /usr/local/bin/kubectl
     update-alternatives --set editor /usr/bin/vim.nox

     # Set the user up
     usermod -aG docker vagrant
     sudo -u vagrant echo 'export MINIKUBE_WANTUPDATENOTIFICATION=false' >> /home/vagrant/.bashrc
     sudo -u vagrant echo 'export KUBECONFIG=$HOME/.kube/config'>>/home/vagrant/.bashrc
     sudo -u vagrant echo 'export MINIKUBE_WANTREPORTERRORPROMPT=false'>>/home/vagrant/.bashrc
     sudo -u vagrant echo 'export MINIKUBE_HOME=$HOME' >> /home/vagrant/.bashrc
     sudo -u vagrant echo 'export CHANGE_MINIKUBE_NONE_USER=true' >> /home/vagrant/.bashrc
     
     # Add bash completion for kubectl
     sudo -u vagrant echo 'source <(kubectl completion bash)' >> /home/vagrant/.bashrc
     sudo -u vagrant mkdir -p /home/vagrant/.kube
     sudo -u vagrant touch /home/vagrant/.kube/config
     sudo -u vagrant mkdir -p /home/vagrant/go/src
     
     # Change sudo to preserve some environments
     echo 'Defaults env_keep+="MINIKUBE_WANTUPDATENOTIFICATION KUBECONFIG MINIKUBE_WANTREPORTERRORPROMPT MINIKUBE_HOME CHANGE_MINIKUBE_NONE_USER"' | EDITOR='tee -a' visudo
     # Install protoc
     curl -fsSLo /tmp/protoc.zip https://github.com/protocolbuffers/protobuf/releases/download/v3.6.1/protoc-3.6.1-linux-x86_64.zip
     unzip /tmp/protoc.zip -x readme.txt -d /usr/local/
     sudo -u vagrant /usr/local/go/bin/go get -u google.golang.org/grpc
     sudo -u vagrant /usr/local/go/bin/go get -u github.com/golang/protobuf/protoc-gen-go
     sudo -u vagrant /usr/local/go/bin/go get -u golang.org/x/lint/golint

     # Setup things for k8s python
     sudo -u vagrant pip3 install kubernetes # Maybe install this in a venv some day.

   SHELL
end
