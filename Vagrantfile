#Credit to https://openstackcookbook.com/

# -*- mode: ruby -*-
# vi: set ft=ruby :
# We set the last octet in IPV4 address here
nodes = {
 'controller' => [1, 200],
 'network' => [1, 202],
 'compute' => [1, 201],
 'swift' => [1, 210],
 'cinder' => [1, 211],
}

Vagrant.configure("2") do |config| 
  # Virtualbox
  config.vm.box = "trusty64"
  config.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
  config.vm.synced_folder ".", "/vagrant", type: "nfs",
  mount_options: ["dmode=775,fmode=664"]
  
  # Default is 2200..something, but port 2200 is used by forescout NAC agent.
  config.vm.usable_port_range= 2800..2900
  
  #Use vagrant username
  #config.ssh.username = "vagrant"
  
  # Use and insert private key
  #config.ssh.keys_only = "true"
  #config.ssh.insert_key = "true"

  #Add new disk
  #file_to_disk = File.realpath( "." ).to_s + "/disk2.vmdk"
    
  nodes.each do |prefix, (count, ip_start)|
    count.times do |i|
      hostname = "%s" % [prefix, (i+1)]

      config.vm.define "#{hostname}" do |box|
        box.vm.hostname = "#{hostname}.book"
        box.vm.network :private_network, ip: "172.16.0.#{ip_start+i}", :netmask => "255.255.0.0"
        box.vm.network :private_network, ip: "172.10.0.#{ip_start+i}", :netmask => "255.255.0.0" 
        box.vm.network :private_network, ip: "192.168.100.#{ip_start+i}", :netmask => "255.255.255.0"
		box.vm.network :public_network, bridge: "Intel(R) Ethernet Connection (2) I219-LM", type: "dhcp"

        # If using Fusion
        box.vm.provider :vmware_fusion do |v|
          v.vmx["memsize"] = 1024
          if prefix == "compute" or prefix == "controller" or prefix == "swift"
            v.vmx["memsize"] = 2048
          end # if
        end # box.vm fusion

        # Otherwise using VirtualBox
        box.vm.provider :virtualbox do |vbox|
          # Defaults
          vbox.customize ["modifyvm", :id, "--memory", 1024]
          vbox.customize ["modifyvm", :id, "--cpus", 1]
          vbox.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
          vbox.customize ["modifyvm", :id, "--nicpromisc4", "allow-all"]
          if prefix == "compute" or prefix == "controller" or prefix == "swift"
            vbox.customize ["modifyvm", :id, "--memory", 4096]
            vbox.customize ["modifyvm", :id, "--cpus", 2]
				#if ARGV[0] == "up" && ! File.exist?(file_to_disk)
		  #vbox.customize ['createhd', '--filename', file_to_disk, '--format', 'VMDK', '--size', 50 * 1024]
		  #vbox.customize ['storageattach', :id, '--storagectl', 'SATAController', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk]
				#end # if
          end # if
        end # box.vm virtualbox
      end # config.vm.define 
    end # count.times
  end # nodes.each
end # Vagrant.configure("2")
