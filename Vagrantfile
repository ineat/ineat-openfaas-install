require 'yaml'

settings = YAML::load(File.read("#{File.dirname(__FILE__)}/vagrant-config.yml"))

Vagrant.configure(2) do |config|
  config.ssh.insert_key = false
  config.vm.define "master" do |master|
    initCluster(master, "master", settings['master'])
    installSwarm(master, settings['master']['ip'], "install_master.yml", "master")
  end

  i = 1
  workers_number = settings["workers"].length

  settings["workers"].each do |worker_name, configuration| 
    config.vm.define worker_name do |worker|
        initCluster(worker, worker_name, configuration)
        installSwarm(worker, configuration["ip"], "install_workers.yml", worker_name)
        i+=1
        if(i == workers_number)
          installOpenfaas(worker) 
        end
    end
  end
end

def initCluster(node, hostname, settings)
  node.vm.hostname = hostname
  node.vm.box = settings["config"]["image"]
  node.vm.network settings["config"]["network"], ip: settings["ip"]
  node.vm.provider :virtualbox do |v|
    v.cpus = settings["config"]["cpus"]
    v.memory = settings["config"]["memory"]
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

def installOpenfaas(node) 
  node.vm.provision "ansible" do |ansible|
    ansible.playbook = "install_openfaas.yml"
    ansible.limit = "master"
    ansible.inventory_path = "hosts.yml"
    ansible.extra_vars = {
      ansible_python_interpreter: "/usr/bin/python3"
    }
  end
end
