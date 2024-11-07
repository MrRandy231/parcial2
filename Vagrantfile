# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    # Configuración de la máquina para el primer servidor Apache
    config.vm.define "apache1" do |apache1|
      apache1.vm.box = "ubuntu/bionic64" # Puedes usar una versión de Ubuntu que prefieras
      apache1.vm.network "private_network", ip: "192.168.33.10"
      apache1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      apache1.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
      SHELL
      apache1.vm.synced_folder ".", "/var/www/html", type: "rsync"
    end
  
    # Configuración de la máquina para el segundo servidor Apache
    config.vm.define "apache2" do |apache2|
      apache2.vm.box = "ubuntu/bionic64"
      apache2.vm.network "private_network", ip: "192.168.33.11"
      apache2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      apache2.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
      SHELL
      apache2.vm.synced_folder ".", "/var/www/html", type: "rsync"
    end
  
    # Configuración de la máquina para el servidor Nginx
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/bionic64"
      nginx.vm.network "private_network", ip: "192.168.33.12"
      nginx.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      nginx.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y nginx
        sudo tee /etc/nginx/conf.d/load_balancer.conf > /dev/null <<EOL
  upstream backend {
      server 192.168.33.10;
      server 192.168.33.11;
  }
  
  server {
      listen 80;
  
      location / {
          proxy_pass http://backend;
      }
  }
  EOL
        sudo systemctl restart nginx
      SHELL
    end
  end
  