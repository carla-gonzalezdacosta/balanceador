
Vagrant.configure("2") do |config|

  # el sistema operativo de las VMs
  config.vm.box = "ubuntu/jammy64"

  #Balanceador de carga (Proxy)
  config.vm.define "balanceador" do |node|
    node.vm.hostname = "balanceador"
    node.vm.network "private_network", ip: "192.168.56.10" #ip estatica
    node.vm.network "forwarded_port", guest: 80, host: 8080 #dirige el puerto de la vm al puerto del host
    node.vm.network "forwarded_port", guest: 443, host: 8443 #dirige el puerto de la vm al puerto del host
    node.vm.provider "virtualbox" do |vb|
      vb.name = "balanceador" #nombre virtualbox
      vb.memory = "1024" #RAM
    end
  end

  #servidor web 1
  config.vm.define "frontal1" do |node|
    node.vm.hostname = "frontal1"
    node.vm.network "private_network", ip: "192.168.56.11"
    node.vm.provider "virtualbox" do |vb|
      vb.name = "frontal1"
      vb.memory = "1024"
    end
  end

  #servidor web 2
  config.vm.define "frontal2" do |node|
    node.vm.hostname = "frontal2"
    node.vm.network "private_network", ip: "192.168.56.12"
    node.vm.provider "virtualbox" do |vb|
      vb.name = "frontal2"
      vb.memory = "1024"
    end
  end

  #servidor base de datos mysql
  config.vm.define "back-mysql" do |node|
    node.vm.hostname = "back-mysql"
    node.vm.network "private_network", ip: "192.168.56.13"
    node.vm.provider "virtualbox" do |vb|
      vb.name = "back-mysql"
      vb.memory = "1024"
    end
  end
 
end
