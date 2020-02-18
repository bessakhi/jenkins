# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.insert_key = false
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "4000"]
  end

  config.vm.define "jenkins" do |app|
    app.vm.hostname = "jenkins"
    config.vm.provider :virtualbox do |vb|
      vb.name = "jenkins"
    end
    app.vm.box = "centos/8"
    app.vm.network :private_network, ip: "192.168.1.11"
  end

  config.vm.provision "shell" do |s|
    ssh_prv_key = ""
    ssh_pub_key = ""
    if File.file?("#{Dir.home}/.ssh/id_rsa")
      ssh_prv_key = File.read("#{Dir.home}/.ssh/id_rsa")
      ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    else
      puts "No SSH key found. You will need to remedy this before pushing to the repository."
    end

    s.inline = <<-SHELL
      if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
        echo "SSH keys already provisioned."
        exit 0;
      fi
    echo "SSH key provisioning."
    mkdir -p /home/vagrant/.ssh/
    touch /home/vagrant/.ssh/authorized_keys
    echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
    echo #{ssh_pub_key} > /home/vagrant/.ssh/id_rsa.pub
    chmod 644 /home/vagrant/.ssh/id_rsa.pub
    echo "#{ssh_prv_key}" > /home/vagrant/.ssh/id_rsa
    chmod 600 /home/vagrant/.ssh/id_rsa
    chown -R vagrant:vagrant /home/vagrant
    exit 0
    SHELL
  end

end
