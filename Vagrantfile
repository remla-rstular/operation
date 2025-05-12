# -*- mode: ruby -*-
# vi: set ft=ruby :

# Configuration variables
WORKER_COUNT = 2  # Change this to scale worker nodes up or down
CONTROL_NODE_MEMORY = 4096  # 4GB
WORKER_NODE_MEMORY = 6144    # 6GB

def get_node_ip(node)
  return "192.168.56.#{100 + node}"
end

def provision_ansible_general(defined_node)
  defined_node.vm.provision "ansible" do |ansible|
    ansible.playbook = "general.yaml"
    ansible.extra_vars = {
      nodes: [
        { hostname: "ctrl", ip: get_node_ip(0) },
        *(1..WORKER_COUNT).map { |i| { hostname: "node-#{i}", ip: get_node_ip(i) } }
      ]
    }
  end
end

# Set ansible groups
ansible_groups = {
  "control" => ["ctrl"],
  "workers" => (1..WORKER_COUNT).map { |i| "node-#{i}" }
}

Vagrant.configure("2") do |config|
  # Use bento/ubuntu-24.04 as base box
  config.vm.box = "bento/ubuntu-24.04"
  config.vm.box_check_update = false

  # Control node configuration
  config.vm.define "ctrl" do |ctrl|
    ctrl.vm.hostname = "ctrl"
    
    # Second NIC (host-only for internal communication)
    ctrl.vm.network "private_network", ip: get_node_ip(0)
    
    ctrl.vm.provider "virtualbox" do |vb|
      vb.name = "ctrl"
      vb.memory = CONTROL_NODE_MEMORY
      vb.cpus = 2
    end

    # Provisioning for control node
    provision_ansible_general(ctrl)
    
    ctrl.vm.provision "ansible" do |ansible|
      ansible.playbook = "ctrl.yaml"
      ansible.groups = ansible_groups
    end
  end

  # Worker nodes configuration
  (1..WORKER_COUNT).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.hostname = "node-#{i}"
      
      # Second NIC (host-only for internal communication)
      node.vm.network "private_network", ip: get_node_ip(i)
      
      node.vm.provider "virtualbox" do |vb|
        vb.name = "node-#{i}"
        vb.memory = WORKER_NODE_MEMORY
        vb.cpus = 2
      end

      # Provisioning for worker nodes
      provision_ansible_general(node)
      
      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "node.yaml"
        ansible.groups = ansible_groups
      end
    end
  end
end