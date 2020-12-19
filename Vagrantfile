# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/contrib-buster64"

  # Add memory, required to link Rust binaries.
  config.vm.provider "virtualbox" do |v|
    v.memory = 8192
  end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y docker docker-compose

    gpasswd -a vagrant docker

    apt-get install -y curl

    apt-get install -y python3-pip
    pip3 install pytest pytest-xdist pytest-timeout requests

    echo '127.0.0.1 testrun.localdomain' >> /etc/hosts
  SHELL

  config.vm.provision "shell", :privileged => false, inline: <<-SHELL
    git clone https://github.com/deltachat/deltachat-core-rust.git
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- --default-toolchain none -y
    cd deltachat-core-rust/python
  SHELL
end
