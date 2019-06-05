# -*- mode: ruby -*-
# vi: set ft=ruby :

MACHINES = {
  :server01 => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.50.10', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "ospf"},
                   {ip: '192.168.50.20', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: "ospf"},
                ]
  },
  
  :server02 => {
        :box_name => "centos/7",
        :net => [
                    {ip: '192.168.50.11', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "ospf"},
                    {ip: '192.168.50.21', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: "ospf"},
                ]
  },
  :server03 => {
        :box_name => "centos/7",
        :net => [
                    {ip: '192.168.50.12', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "ospf"},
                    {ip: '192.168.50.22', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: "ospf"},
                ]
  },
}


Vagrant.configure(2) do |config|
  MACHINES.each do |boxname, boxconfig|
  
    config.vm.define boxname do |box|
      
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s
      
      boxconfig[:net].each do |ipconf|
        box.vm.network "private_network", ipconf
      end
              
      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end

#  config.vm.provision "ansible" do |ansible|
#    ansible.verbose = "vvv"
#    ansible.playbook = "provisioning/playbook.yml"
#    ansible.sudo = "true"
#  end

      config.vm.provision "shell", inline: <<-SHELL
        set -x
        sudo setenforce 0
        yum install -y quagga
        echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
        sysctl -p /etc/sysctl.conf
        cp /usr/share/doc/quagga-0.99.22.4/zebra.conf.sample /etc/quagga/zebra.conf
        echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
        systemctl restart network
      SHELL

      case boxname.to_s
      when "server01"
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          cp -f /vagrant/configs/server01/* /etc/quagga/
          chown quagga:quaggavt /etc/quagga/*.conf
          systemctl enable zebra.service 
          systemctl start zebra.service
          systemctl enable ospfd.service
          systemctl start ospfd.service
          SHELL
      when "server02"
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          cp -f /vagrant/configs/server02/* /etc/quagga/
          chown quagga:quaggavt /etc/quagga/*.conf
          systemctl enable zebra.service 
          systemctl start zebra.service
          systemctl enable ospfd.service
          systemctl start ospfd.service
          SHELL
      when "server03"
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          cp -f /vagrant/configs/server03/* /etc/quagga/
          chown quagga:quaggavt /etc/quagga/*.conf
          systemctl enable zebra.service 
          systemctl start zebra.service
          systemctl enable ospfd.service
          systemctl start ospfd.service
          SHELL
      end
    end
  end
end

#  config.vm.provider "virtualbox" do |v|
#    v.memory = 256
#  end

# config.vm.define "server01" do |ser01|
#    ser01.vm.network "private_network", ip: "192.168.50.10", virtualbox__intnet: "dns"
#    ser01.vm.hostname = "server01"
#  end

#  config.vm.define "server02" do |ser02|
#    ser02.vm.network "private_network", ip: "192.168.50.11", virtualbox__intnet: "dns"
#    ser02.vm.hostname = "server02"
#  end

#  config.vm.define "server03" do |ser03|
#    ser03.vm.network "private_network", ip: "192.168.50.15", virtualbox__intnet: "dns"
#    ser03.vm.hostname = "server03"
#  end
#end