# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure(2) do |config|

  config.ssh.forward_agent = true
  config.vm.define 'axe-build', autostart: false do |gitian|
    required_plugins = %w( vagrant-vbguest vagrant-disksize )
    _retry = false
    required_plugins.each do |plugin|
        unless Vagrant.has_plugin? plugin
            system "vagrant plugin install #{plugin}"
            _retry=true
        end
    end

    if (_retry)
        exec "vagrant " + ARGV.join(' ')
    end
    gitian.disksize.size = "25GB"
    gitian.vm.box = "ubuntu/bionic64"
    gitian.vm.network "forwarded_port", guest: 22, host: 2200, auto_correct: true
    gitian.vm.provision "ansible" do |ansible|
      ansible.playbook = "gitian.yml"
      ansible.verbose = 'v'
      ansible.raw_arguments = Shellwords.shellsplit(ENV['ANSIBLE_ARGS']) if ENV['ANSIBLE_ARGS']
    end
    gitian.vm.provider "virtualbox" do |v|
      v.name = "axe-build"
      v.memory = 4096
      v.cpus = 2
    end
#    gitian.vm.synced_folder "~/.gnupg", "/home/vagrant/.gnupg", type: "sshfs"
#    gitian.vm.synced_folder "./gitian.sigs", "/home/vagrant/gitian.sigs", create: true
#    gitian.vm.synced_folder "./axe-binaries", "/home/vagrant/axe-binaries", create: true
    gitian.vm.post_up_message = "AXE deterministic build environment started."
  end

end
