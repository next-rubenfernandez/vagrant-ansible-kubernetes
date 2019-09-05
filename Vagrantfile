# -*- mode: ruby -*-
# vi: set ft=ruby :
N = 2

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 2
    v.customize [
          "modifyvm", :id,
          "--audio", "none"
        ]
  end

  config.vm.define "k8s-master" do |master|
    master.vm.box = "centos/7"
    master.vm.network "private_network", ip: "192.168.50.10"
    master.vm.hostname = "k8s-master"
    master.vm.provision "ansible" do |ansible|
      ansible.playbook = "kubernetes-ansible/kubernetes.yml"
      ansible.groups = {
          "master" => ["k8s-master"],
          "master:vars" => { "product" => "k8s-master" },
          "all:children" => ["master"]
        }
      ansible.extra_vars = {
        node_ip: "192.168.50.10",
      }
    end
  end
    
  (1..N).each do |i|
    config.vm.define "k8s-node-#{i}" do |node|
      node.vm.box = "centos/7"
      node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
      node.vm.hostname = "k8s-node-#{i}"
      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "kubernetes-ansible/kubernetes.yml"
        ansible.groups = {
          "nodes" => ["k8s-nodes"],
          "nodes:vars" => { "product" => "k8s-nodes" },
          "all:children" => ["nodes"]
        }
        ansible.extra_vars = {
          node_ip: "192.168.50.#{i + 10}",
        }
      end
    end
  end
end