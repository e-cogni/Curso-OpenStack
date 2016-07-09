# -*- mode: ruby -*-
# vi: set ft=ruby :

nodes = {
    'controller'  => [1, 200],
    'network'  => [1, 202],
    'compute'  => [1, 201],
    # 'swift'   => [1, 210],
    'cinder'   => [1, 211],
}
#
Vagrant.require_version ">= 1.7.1"

Vagrant.configure("2") do |config|
    
	config.vm.box = "e-cogni/xenial64"
	config.vm.box_url = "https://github.com/e-cogni/vagrant-ubuntu/releases/download/16.04/ubuntu-xenial64-pt_br.box"
	#config.vm.box_url = "https://github.com/e-cogni/vagrant-ubuntu/blob/master/metadata.json"
	#config.vm.box_url = "file:///home/aluno/teste_OpenStack/box/ubuntu-xenial64-pt_br.box"
	config.vm.usable_port_range = 2800..2900 
	config.vm.boot_timeout = 600
 	config.vm.synced_folder ".", "/vagrant", type: "nfs"

		if Vagrant.has_plugin?("vagrant-cachier")
			config.cache.scope = :box
			config.cache.enable :apt
		else
			puts "[-] WARN: This would be much faster if you ran: 'vagrant plugin install vagrant-cachier' first"
		end

	nodes.each do |prefix, (count, ip_start)|
		count.times do |i|
		hostname = "%s" % [prefix, (i+1)]
		config.vm.define "#{hostname}" do |box|
			box.vm.hostname = "#{hostname}.local"
			box.vm.network :private_network, ip: "172.17.0.#{ip_start+i}", :netmask => "255.255.0.0"
			box.vm.network :private_network, ip: "10.100.0.#{ip_start+i}", :netmask => "255.255.0.0" 
			box.vm.network :private_network, ip: "192.168.168.#{ip_start+i}", :netmask => "255.255.255.0" 

			#box.vm.provision :shell, :path => "#{prefix}.sh"
                
			box.vm.provider :virtualbox do |vbox|
			vbox.customize ["modifyvm", :id, "--memory", 1024]
			vbox.customize ["modifyvm", :id, "--cpus", 1]
			vbox.customize ["modifyvm", :id, "--name", "#{hostname}"]
			vbox.customize ["modifyvm", :id, "--groups", "/OpenStack"]
				if prefix == "compute" or prefix == "controller" or prefix == "swift"
					vbox.customize ["modifyvm", :id, "--memory", 2048]
					vbox.customize ["modifyvm", :id, "--cpus", 2]
					vbox.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
					vbox.customize ["modifyvm", :id, "--nicpromisc4", "allow-all"]
				end
			end
    		end
	end
end
#
end
