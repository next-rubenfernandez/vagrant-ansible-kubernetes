# -*- mode: ruby -*-
# vi: set ft=ruby :

nodes = [
  { :hostname => 'kubernetes-master', :ip => '192.168.10.2', :ram => 2048 },
  { :hostname => 'kubernetes-node1', :ip => '192.168.10.3', :ram => 1024 },
  { :hostname => 'kubernetes-node2', :ip => '192.168.10.4', :ram => 1024 },
  { :hostname => 'kubernetes-node3', :ip => '192.168.10.5', :ram => 1024 }
]

Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = "centos/7";
      nodeconfig.vm.box_check_update = false
      nodeconfig.vm.hostname = node[:hostname] + ".box"
      nodeconfig.vm.network :private_network, ip: node[:ip]
      nodeconfig.vm.network "public_network"
      memory = node[:ram] ? node[:ram] : 256;
      
      nodeconfig.vm.provider :virtualbox do |vb|
        vb.customize [
          "modifyvm", :id,
          "--memory", memory.to_s,
          "--cpus", "4",
          "--audio", "none"
        ]
      end

      nodeconfig.vm.provision "shell",
        inline: "yum update -y"

      nodeconfig.vm.provision :ansible do |ansible|
        ansible.playbook = "kubernetes-ansible/kubernetes.yml"
        ansible.groups = {
          "master" => ["kubernetes-master"],
          "master:vars" => { "product" => "master" },
          "nodes" => ["kubernetes-node1","kubernetes-node2","kubernetes-node3"],
          "nddes:vars" => { "product" => "nodes" },
          "all:children" => ["master", "nodes"]
        }
      end

    end
  end
end