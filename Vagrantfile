# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # 创建3台虚拟机
  vmnodes = ["master", "node1", "node2"]
  vmnodes.each_index do |index|
    config.vm.define "#{vmnodes[index]}" do |vmnode|
      # 设置Box
      vmnode.vm.box = "bento/centos-7.4"

      # 设置主机名
      vmnode.vm.hostname = "#{vmnodes[index]}"
      # 设置IP
      vmnode.vm.network "private_network", ip: "192.168.100.10#{index}"

      # VirtaulBox虚拟机配置
      vmnode.vm.provider "virtualbox" do |vb|
          vb.gui = false
          # 设置虚拟机的名称
          vb.name = "k8s-#{vmnodes[index]}"
          # 设置虚拟机的CPU个数
          vb.cpus = 2
          # 设置虚拟机的内存大小  
          vb.memory = 2048
      end

      # 设置主机与虚拟机的共享目录， 根据需要设置
      # vmnode.vm.synced_folder "f:/vms-share", "/vms-share"

      # shell脚本进行配置
      vmnode.vm.provision "shell", inline: <<-SHELL
          # Install docker using the convenience script, 参看docker官方文档, 这部分内容需要改进
          wget -qO- https://get.docker.com/ | sh
          usermod -aG docker vagrant
      SHELL
    end
  end
end
