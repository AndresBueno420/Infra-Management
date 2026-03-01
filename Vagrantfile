# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  # --- VM 1: Gateway (HAProxy) ---
  config.vm.define "proxy" do |proxy|
    proxy.vm.hostname = "proxy-server"
    proxy.vm.network "private_network", ip: "192.168.56.10"
    # Redirección para acceder al proyecto desde tu navegador en el Host
    proxy.vm.network "forwarded_port", guest: 80, host: 8080
    proxy.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
    end
  end

  # --- VM 2: Frontend (Nginx + Vue) ---
  config.vm.define "frontend" do |front|
    front.vm.hostname = "frontend-server"
    front.vm.network "private_network", ip: "192.168.56.11"
    front.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
    end
  end

  # --- VM 3: Auth & Users (Go & Java) ---
  config.vm.define "auth-users" do |au|
    au.vm.hostname = "auth-users-server"
    au.vm.network "private_network", ip: "192.168.56.12"
    au.vm.provider "virtualbox" do |vb|
      vb.memory = "2048" 
      vb.cpus = 2
    end
  end

  # --- VM 4: TODOs & Log Processor (Node & Python) ---
  config.vm.define "todos-logic" do |tl|
    tl.vm.hostname = "todos-logic-server"
    tl.vm.network "private_network", ip: "192.168.56.13"
    tl.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
    end
  end

  # --- VM 5: Data (Redis & DB) ---
  config.vm.define "data" do |data|
    data.vm.hostname = "data-server"
    data.vm.network "private_network", ip: "192.168.56.14"
    data.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
    end
  end

  config.ssh.insert_key = false
end