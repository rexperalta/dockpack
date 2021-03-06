# -*- mode: ruby -*-
# vi: set ft=ruby :
# To use these virtual machine install Vagrant and VirtuaBox or VMWare.
# vagrant up [centos6|fedora22|kalu|ubuntu41|coreos|windows]

Vagrant.configure(2) do |config|

  # provision.yml runs on a vagrant up, packer.yml runs on packer build.
  config.vm.provision "ansible" do |ansible|
    ansible.inventory_path = "ansible/inventory/ansible.ini"
    ansible.playbook = "ansible/provision.yml"
    ansible.verbose = "vv"
   end

  # Prefer VirtualBox before VMware Fusion
  config.vm.provider "virtualbox"
  config.vm.provider "vmware_fusion"

  config.vm.provider "virtualbox" do |virtualbox|
    virtualbox.gui = false
    virtualbox.customize ["modifyvm", :id, "--memory", 2048]
  end

  config.vm.provider "vmware_fusion" do |vmware|
    vmware.gui = true
    vmware.vmx["memsize"] = "2048"
    vmware.vmx["numvcpus"] = "2"
  end

  config.ssh.insert_key = false
  config.vm.box_check_update = false
  # disable guest additions
  config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true

  # Apple OS X: Get a life, get a Mac! Implementation details for osx packer boxes:
  # https://github.com/boxcutter/osx

  # Windows: You need to create a temporarily licenced Windows box with 'packer build windows.json'
  config.vm.define :windows, autostart: true do |windows_config|
    windows_config.vm.box = "windows"
    windows_config.vm.communicator = "winrm"

    windows_config.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
    windows_config.vm.network :forwarded_port, guest: 22, host: 2200, id: "ssh", auto_correct: true

    # Admin user name and password
    windows_config.winrm.username = "vagrant"
    windows_config.winrm.password = "vagrant"

    windows_config.vm.guest = :windows
    windows_config.windows.halt_timeout = 15

    windows_config.vm.provider :virtualbox do |vb, override|
        # Use Remote Desktop Protocol
        vb.gui = false
        vb.name = "windows"
        vb.customize ["modifyvm", :id, "--memory", 2048]
        vb.customize ["modifyvm", :id, "--cpus", 2]
        vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
    end

    windows_config.vm.provider :vmware_fusion do |vm, override|
        #vm.gui = true
        vm.vmx["memsize"] = "2048"
        vm.vmx["numvcpus"] = "2"
        vm.vmx["ethernet0.virtualDev"] = "vmxnet3"
        vm.vmx["RemoteDisplay.vnc.enabled"] = "false"
        vm.vmx["RemoteDisplay.vnc.port"] = "5900"
        vm.vmx["scsi0.virtualDev"] = "lsisas1068"
    end

    windows_config.vm.provider :vmware_workstation do |vm, override|
        #vm.gui = true
        vm.vmx["memsize"] = "2048"
        vm.vmx["numvcpus"] = "2"
        vm.vmx["ethernet0.virtualDev"] = "vmxnet3"
        vm.vmx["RemoteDisplay.vnc.enabled"] = "false"
        vm.vmx["RemoteDisplay.vnc.port"] = "5900"
        vm.vmx["scsi0.virtualDev"] = "lsisas1068"
    end
  end


  # Centos 6: Create the DISO STIG hardened centos6 box with 'packer build centos6.json'
  config.vm.define :centos6, autostart: true do |centos6_config|
    centos6_config.vm.box = "dockpack/centos6"
    centos6_config.vm.box_url ="https://atlas.hashicorp.com/dockpack/boxes/centos6"
    centos6_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2201, auto_correct: true

    centos6_config.vm.provider "vmware_fusion" do |vmware|
      vmware.vmx["memsize"] = "2048"
      vmware.vmx["numvcpus"] = "2"
    end
    centos6_config.vm.provider "virtualbox" do |vb|
      vb.name = "centos6"
    end
  end

  ## Ubuntu Official: https://cloud-images.ubuntu.com/vagrant/
  config.vm.define :ubuntu14, autostart: true do |ubuntu14_config|
    ubuntu14_config.vm.box = "ubuntu14"
    ubuntu14_config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
    ubuntu14_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2202, auto_correct: true

    ubuntu14_config.vm.provider "vmware_fusion" do |vmware|
      vmware.vmx["memsize"] = "2048"
      vmware.vmx["numvcpus"] = "2"
    end
    ubuntu14_config.vm.provider "virtualbox" do |virtualbox|
      virtualbox.name = "ubuntu14"
      virtualbox.gui = true
    end
  end

  ## Chef's Bento project on Hashicorp Atlas: Bento project https://github.com/chef/bento
  # Just an example of a linux from this project
  config.vm.define :fedora22, autostart: true do |fedora22_config|
    fedora22_config.vm.box = "dockpack/fedora22"
    fedora22_config.vm.box_url = "https://atlas.hashicorp.com/dockpack/boxes/fedora22"
    fedora22_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2203, auto_correct: true

    fedora22_config.vm.provider "vmware_fusion" do |vmware|
      vmware.vmx["memsize"] = "2048"
      vmware.vmx["numvcpus"] = "2"
    end
    fedora22_config.vm.provider "virtualbox" do |vb|
      vb.name = "fedora22"
    end
  end

  ## CoreOS Official: https://github.com/coreos/coreos-vagrant
  config.vm.define :coreos, autostart: true do |coreos_config|
    coreos_config.vm.box = "coreos-box"  # to delete: 'vagrant destroy; box remove coreos-box'

    coreos_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2204, auto_correct: true
    coreos_config.vm.provider "vmware_fusion" do |vmware, override|
      override.vm.box_url = "http://stable.release.core-os.net/amd64-usr/current/coreos_production_vagrant_vmware_fusion.box"
    end
    coreos_config.vm.provider "virtualbox" do |virtualbox, override|
      override.vm.box_url = "http://stable.release.core-os.net/amd64-usr/current/coreos_production_vagrant.box"
      virtualbox.name = "coreos"
    end
  end

  ## Kali linux
  config.vm.define "kali", autostart: false do |kali_config|
    kali_config.vm.box = "dockpack/kali"
    kali_config.vm.box_url ="https://atlas.hashicorp.com/dockpack/boxes/kali"
    kali_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2205, auto_correct: true

    kali_config.vm.provider "vmware_fusion" do |vmware|
      vmware.gui = true
      vmware.vmx["memsize"] = "2048"
      vmware.vmx["numvcpus"] = "2"
    end
    kali_config.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.name = "kali"
    end
  end

  config.vm.define :centos7, autostart: true do |centos7_config|
    centos7_config.vm.box = "dockpack/centos7"
    centos7_config.vm.box_url = "https://atlas.hashicorp.com/dockpack/boxes/centos7"
    centos7_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2207, auto_correct: true

    centos7_config.vm.provider "vmware_fusion" do |vmware|
      vmware.vmx["memsize"] = "2048"
      vmware.vmx["numvcpus"] = "2"
    end
    centos7_config.vm.provider "virtualbox" do |vb|
      vb.name = "centos7"
    end
  end
end

