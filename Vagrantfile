# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  # Nodo 1: Tráfico y APIs Ligeras
  config.vm.define "node-apps" do |app|
    app.vm.hostname = "node-apps"
    app.vm.network "private_network", ip: "192.168.56.10"
    app.vm.network "forwarded_port", guest: 80, host: 8080
    app.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end

  # Nodo 2: Datos, Trazabilidad y Java
  config.vm.define "node-data" do |data|
    data.vm.hostname = "node-data"
    data.vm.network "private_network", ip: "192.168.56.11"
    data.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end

  config.ssh.insert_key = false
end