# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  nodes = ["node-1", "node-2"]
  nodes.each_with_index do |item, i|
    config.vm.define item do |node|
      node.vm.box = "rockylinux/9"
      node.vm.box_check_update = false
      node.vm.hostname = item
      node.vm.network "private_network", ip: "192.168.56.#{101 + i}" # s_lan
      node.vm.network "private_network", ip: "192.168.57.#{101 + i}", virtualbox__intnet: "d_lan"
      node.vm.network "private_network", ip: "192.168.58.#{101 + i}", virtualbox__intnet: "ic_lan1"
      node.vm.network "private_network", ip: "192.168.59.#{101 + i}", virtualbox__intnet: "ic_lan2"
      node.vm.network "private_network", ip: "192.168.60.#{101 + i}", virtualbox__intnet: "a_lan"
      node.vm.synced_folder ".", "/vagrant", disabled: true
      node.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
        vb.cpus = 1
      end
      if item.equal?(nodes.last)
        node.vm.provision "ansible" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "setup.yml"
        end
      end
    end
  end
end
