# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|

  config.vm.define 'mssqlsrvr1_dbsrvr' do |machine|
    machine.vm.box = "centos/7"
    
    config.vm.provider "virtualbox" do |vb|
     vb.memory = "4096"
    end
    
    # Setting private ip for this machine
    machine.vm.network :private_network, ip: '192.168.88.22'
    # Port forwarding to connect to VM via localhost (127.0.0.1)
    machine.vm.network "forwarded_port", guest: 1433, host: 11443 #Access SQL Server from Development host (desktop or laptop)
    
    # Setting the hostname of the VM
    machine.vm.hostname = 'mssqlsrvr1'

    # Setting the test Playbook for Ansible and Inventory
    machine.vm.provision 'ansible' do |ansible|
      ansible.playbook = 'tests/playbook.yml'
      ansible.inventory_path = 'tests/inventory'
      ansible.extra_vars = {sa_password: "My?Password12345", accept_eula: "No", edition: "1"}
    end

  end
end