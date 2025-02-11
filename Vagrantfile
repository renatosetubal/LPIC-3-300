# -*- mode: ruby -*-
# vi: set ft=ruby :
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
require 'yaml'
secmachines = YAML.load_file("machines.yml")

Vagrant.configure("2") do |config|
  secmachines.each do |machines|
    config.vm.define machines["name"] do |server|
      server.vm.hostname = machines["name"]
      server.vm.box = machines["box"]
      server.vm.box_version["boxversion"]
      server.vm.box_check_update = false
      server.vbguest.auto_update = false
      #Definir interfaces de rede
      if machines["netbr"]
          machines["netbr"].each do |netbr|
            if netbr["tiporede"] == "private_network"
              server.vm.network netbr["tiporede"], ip: netbr["ipfixo"], dns: netbr["dns"] 
            else

              if netbr["tipoconexao"] == "dhcp"
                server.vm.network netbr["tiporede"], bridge: netbr["adaptador"],  type: netbr["tipoconexao"], dns: netbr["dns"]
              else
                server.vm.network netbr["tiporede"], bridge: netbr["adaptador"],  ip: netbr["ipfixo"], dns: netbr["dns"]
              end
            end
          end
      end  
      #Mapear as portas guestxhost
      if machines['ports'] 
          machines["ports"].each do |port|
            if port["status"] == true
               server.vm.network "forwarded_port", guest: port["guest"], host: port["host"]
            end   
        end
      end  
      #Preparar discos extras
      if machines['disks']
        machines["disks"].each do |disc|
          if disc["status"] == true
            server.vm.disk :disk, size: disc['size'], name: disc['name']
          end
      end
    end  
    server.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--groups", "/Lpic300"]
      vb.memory = machines["memory"]
      vb.cpus = machines["cpus"]
      vb.name = machines["name"]
    end
    if machines['script']
      server.vm.provision "shell", path: machines["script"]
     end
      # if machines['folder']
      #   server.vm.synced_folder machines['folder'], "/var/www/"  
      # end  
    end
  end
end
