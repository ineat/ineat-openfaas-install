master_ip = "10.0.3.2"
master_config = {"cpus" => 1, "memory" => 512}
workers_ip_address_pattern = "10.0.3"
workers_number = 2
workers_config = {"cpus" => 1, "memory" => 512}

Vagrant.configure(2) do |config|
  config.ssh.insert_key = false
  
  config.vm.define "master" do |master|
    initCluster(master, "master", "ubuntu/xenial64", "private_network", master_ip, master_config)
    installSwarm(master, master_ip, "install_master.yml", "master")
  end
  

  (1..workers_number).each do |i| 
    config.vm.define "worker-#{i}" do |worker|
        ip = "#{workers_ip_address_pattern}.#{i+2}"
        initCluster(worker, "worker-#{i}", "ubuntu/xenial64", "private_network", ip, workers_config)
        installSwarm(worker, ip, "install_workers.yml", "worker-#{i}")

        if(i == workers_number)
          worker.vm.provision "ansible" do |ansible|
            ansible.playbook = "install_openfaas.yml"
            ansible.limit = "master"
            ansible.inventory_path = "hosts.yml"
            ansible.extra_vars = {
              ansible_python_interpreter: "/usr/bin/python3"
            }
          end
        end
    end
  end
end

def initCluster(node, hostname, image, network, ip, config)
  node.vm.hostname = hostname
  node.vm.box = image
  node.vm.network network, ip: ip
  node.vm.provider :virtualbox do |v|
    v.cpus = config["cpus"]
    v.memory = config["memory"]
  end
end

def installSwarm(node, ip, playbook, limit)
  node.vm.provision "ansible" do |ansible|
    ansible.playbook = playbook
    ansible.limit = limit
    ansible.extra_vars = {
        ansible_python_interpreter: "/usr/bin/python3",
        ip: ip
    }
  end
  
end
