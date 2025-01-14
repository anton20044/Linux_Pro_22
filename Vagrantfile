MACHINES = {
  :router1 => {
        :box_name => "jammy",
        :vm_name => "router1",
        :net => [
                   ["10.0.10.1", 2, "255.255.255.252", "r1-r2"],
                   ["10.0.12.1", 3, "255.255.255.252", "r1-r3"],
                   ["192.168.10.1", 4, "255.255.255.0", "net1"],
                   ["192.168.56.10", 5],
                ]
  },

  :router2 => {
        :box_name => "jammy",
        :vm_name => "router2",
        :net => [
                   ["10.0.10.2", 2, "255.255.255.252", "r1-r2"],
                   ["10.0.11.2", 3, "255.255.255.252", "r2-r3"],
                   ["192.168.20.1", 4, "255.255.255.0", "net2"],
                   ["192.168.56.11", 5],
                ]
  },

  :router3 => {
        :box_name => "jammy",
        :vm_name => "router3",
        :net => [
                   ["10.0.11.1", 2, "255.255.255.252", "r2-r3"],
                   ["10.0.12.2", 3, "255.255.255.252", "r1-r3"],
                   ["192.168.30.1", 4, "255.255.255.0", "net3"],
                   ["192.168.56.12", 5],
                ]
  }

}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|
    
    config.vm.define boxname do |box|
   
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]
  
      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config.d/60-cloudimg-settings.conf        
        systemctl restart sshd.service
      SHELL


      if boxconfig[:vm_name] == "router3"
       box.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/provision.yml"
        ansible.inventory_path = "ansible/hosts"
        ansible.host_key_checking = "false"
        ansible.limit = "all"
       end
      end

      boxconfig[:net].each do |ipconf|
        box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], virtualbox__intnet: ipconf[3])
      end

     end
  end
end
