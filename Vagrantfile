IMAGE_NAME = "ubuntu/focal64"
MASTER_NODE_IP = ["192.168.56.2"]
# MASTER_NODE_IP = ["192.168.56.2", "192.168.56.5"]
MASTER_SSH_FORWARDED_PORT = 2722
# WORKER_NODE_IPS = ["192.168.56.3", "192.168.56.4", "192.168.56.6"]
WORKER_NODE_IPS = ["192.168.56.3", "192.168.56.4"]
# LOADBALANCER_IP = "192.168.56.10"

Vagrant.configure(2) do |config|
    # Configure box
    config.vm.box = IMAGE_NAME
    config.vm.box_check_update = false
    config.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", disabled: true

    # Provision Master Node
    MASTER_NODE_IP.each_with_index do |master_node_ip, index|
        masterhostname = "Master-#{'%02d' % (index + 1)}"
        config.vm.define "#{masterhostname}" do |master|
            master.vm.provider "virtualbox" do |v|
                v.memory = 4096
                v.cpus = 2
                v.name = "k8s-#{masterhostname}"
            end
            master.vm.hostname = "#{masterhostname}"
            master.vm.network "private_network", ip: master_node_ip
            master.vm.network "forwarded_port", guest: 22, host: MASTER_SSH_FORWARDED_PORT, auto_correct: true
            master.vm.network "forwarded_port", guest: 6443, host: 6443, auto_correct: true
        end
    end
    # Provision worker nodes
    WORKER_NODE_IPS.each_with_index do |node_ip, index|
        hostname = "worker-#{'%02d' % (index + 1)}"
        forwarded_port = MASTER_SSH_FORWARDED_PORT + index + 1
        config.vm.define "#{hostname}" do |worker|
            worker.vm.provider "virtualbox" do |v|
                v.memory = 2048
                v.cpus = 2
                v.name = "k8s-#{hostname}"
            end
            worker.vm.hostname = "#{hostname}"
            worker.vm.network "private_network", ip: node_ip
            worker.vm.network "forwarded_port", guest: 22, host: forwarded_port, auto_correct: true
        end
    end
end


  # Provision Load Balancer Node
#   config.vm.define "lb" do |node|
#     node.vm.provider "virtualbox" do |vb|
#       vb.name = "lb"
#       vb.memory = 512
#       vb.cpus = 1
#     end
#     node.vm.hostname = "lb"
#     node.vm.network :private_network, ip: LOADBALANCER_IP
# 	  node.vm.network "forwarded_port", guest: 22, host: 2730, auto_correct: true
#     node.vm.provision "setup-hosts", :type => "shell", :path => "ubuntu/setup-hosts.sh" do |s|
#       s.args = ["enp0s8"]
#     end
#     node.vm.provision "setup-dns", type: "shell", :path => "ubuntu/update-dns.sh"
#     node.vm.provision "setup-dns", type: "shell", :path => "ubuntu/setup-ssh.sh", privileged: false
#   end


# Vagrant.configure("2") do |config|

#   config.vm.define "loadbalancer" do |loadbalancer|
#     loadbalancer.vm.box = 'ubuntu/bionic64'
#     loadbalancer.vm.hostname = "loadbalancer"
#     loadbalancer.vm.network :private_network, ip: LOADBALANCER_IP
#     loadbalancer.vm.network "forwarded_port", guest: 80, host: 3000
#     loadbalancer.vm.network "forwarded_port", guest: 22, host: 3000, auto_correct: true

#   end
# end