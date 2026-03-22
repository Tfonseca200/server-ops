Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2204"

  config.vm.provision "shell", inline: <<-SHELL
      apt-get update -y 
      apt-get upgrade -y
      apt-get install vim htop
  SHELL

  config.vm.provider "libvirt" do |lv|
    lv.uri = "qemu:///system"
    lv.cpu_mode = "host-passthrough"
    lv.management_network_name = "default"
  end


  # VM GATEWAY - DNS - FIREWALL - ROUTE
  config.vm.define "server-gateway" do |gw|
    gw.vm.hostname = "server-gateway"
    
    gw.vm.network "private_network", 
      ip: "192.168.10.2", 
      libvirt__network_name: "networking-client",
      libvirt__network_address: "192.168.10.0/24"

    gw.vm.network "private_network",
      ip: "10.10.0.2",
      libvirt__network_name: "networking-internal",
      libvirt__network_address: "10.10.0.0/24"

    gw.vm.provider "libvirt" do |lv|
      lv.memory = 768
      lv.cpus = 1
      lv.storage :file, size: '5G', device: 'vdb'
    end
  end


  # SERVER WEB
  config.vm.define "server-web" do |app|
    app.vm.hostname = "server-web"

    app.vm.network "private_network", 
      ip: "10.10.0.40",
      libvirt__network_name: "networking-internal"

     app.vm.provider "libvirt" do |lv|
      lv.memory = 1024
      lv.cpus = 1
      lv.storage :file, size: '5G', device: 'vdb'
    end
  end

  # VM SERVER DATABASE
  config.vm.define "server-database" do |db|
     db.vm.hostname = "server-database"
     db.vm.network "private_network",
      ip: "10.10.0.20",
      libvirt__network_name: "networking-internal"

     db.vm.provider "libvirt" do |lv|
      lv.memory = 1536
      lv.cpus = 1
      lv.storage :file, size: '10G', device: 'vdb'
    end
  end
end
