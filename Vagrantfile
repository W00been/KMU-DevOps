# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # 전역 플러그인 설정
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end

  # GitLab VM 정의
  config.vm.define "gitlab" do |cfg|
    cfg.vm.box = "ubuntu/jammy64"
    cfg.vm.hostname = "gitlab.local"
    cfg.vm.network "private_network", ip: "192.168.33.10"
    cfg.vm.network "forwarded_port", guest: 80, host: 8080
    cfg.vm.network "forwarded_port", guest: 22, host: 8022
    cfg.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: [".git/"]

    cfg.vm.provider "virtualbox" do |vb|
      vb.name = "gitlab"
      vb.memory = "4096"
    end

    cfg.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y curl openssh-server ca-certificates

    debconf-set-selections <<< "postfix postfix/mailname string $HOSTNAME"
    debconf-set-selections <<< "postfix postfix/main_mailer_type string 'Internet Site'"
    DEBIAN_FRONTEND=noninteractive sudo apt-get install -y postfix

    if [ ! -e /vagrant/gitlab-ce.deb ]; then
      wget --content-disposition "https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/jammy/gitlab-ce_16.10.0-ce.0_amd64.deb/download.deb" -O /vagrant/gitlab-ce.deb
    fi
    
    sudo dpkg -i /vagrant/gitlab-ce.deb
    sudo gitlab-ctl reconfigure
    SHELL
  end

  # CentOS VM 정의
  config.vm.define "vm-1" do |cfg|
    cfg.vm.box = "generic/centos8"
    cfg.vm.hostname = "vm1-centos8"
    cfg.vm.network "private_network", ip: "192.168.33.11"

    cfg.vm.provider "virtualbox" do |vb|
      vb.name = "vm-1"
      vb.cpus = 2
      vb.memory = "2048"
    end
  end

end
