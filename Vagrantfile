VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu/trusty64"

  config.ssh.forward_agent = true
  
  config.vm.provider "virtualbox" do |v|
    v.memory = 8192
  end

  config.vm.define "server1" do |server|
    server.vm.network "private_network", ip: "10.33.33.4"
    server.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", disabled: true
    server.vm.network :forwarded_port, guest: 22, host: 2230, auto_correct: true
  end
  
  config.vm.provision "ansible" do |ansible|
    #ansible.verbose = "vvvv"
    ansible.playbook = "ansible/site.yml"
    ansible.inventory_path = "ansible/vm"
    ansible.limit = "all"
  end

end
