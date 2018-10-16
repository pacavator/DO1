# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
NODE_COUNT = 3
TOMCAT_COUNT = (NODE_COUNT - 1)


Vagrant.configure("2") do |config| 
  config.vm.box = "bento/centos-7.5"
  config.vm.provider "virtualbox" do |vb|
      vb.gui = true
  end
 

    
  
  config.vm.define "apache" do |apache|
    apache.vm.hostname = "apache"
    apache.vm.network "private_network", ip: "192.168.10.10"
    apache.vm.network "forwarded_port", guest: 80, host: 18000
    apache.vm.provision "shell", inline: <<-SHELL
      TOMCAT_COUNT=#{TOMCAT_COUNT}
      echo $TOMCAT_COUNT
      yum -y install mc
      yum -y install httpd
      systemctl enable httpd
      cp /vagrant/mod_jk.so /etc/httpd/modules/
      touch /etc/httpd/conf/workers.properties
      echo "worker.list=lb" >> /etc/httpd/conf/workers.properties
      echo "worker.lb.type=lb" >> /etc/httpd/conf/workers.properties
      echo "worker.lb.balance_workers=tomcat1,tomcat2 other" >> /etc/httpd/conf/workers.properties
      for ((i=1;i<=$TOMCAT_COUNT;i++))
        do
          echo "worker.tomcat${i}.host=tomcat${i}" >> /etc/httpd/conf/workers.properties
          echo "worker.tomcat${i}.port=8009" >> /etc/httpd/conf/workers.properties
          echo "worker.tomcat${i}.type=ajp13" >> /etc/httpd/conf/workers.properties
          echo "192.168.10.1${i}    tomcat${i}" >> /etc/hosts
        done
      echo "*********************************"             
      echo "LoadModule jk_module modules/mod_jk.so" >> /etc/httpd/conf/httpd.conf
      echo "JkWorkersFile conf/workers.properties" >> /etc/httpd/conf/httpd.conf
      echo "JkShmFile /tmp/shm" >> /etc/httpd/conf/httpd.conf
      echo "JkLogFile logs/mod_jk.log" >> /etc/httpd/conf/httpd.conf
      echo "JkLogLevel info" >> /etc/httpd/conf/httpd.conf
      echo "JkMount /pacavaca* lb" >> /etc/httpd/conf/httpd.conf	        
      systemctl start httpd 
    SHELL
  end
  
  (1..TOMCAT_COUNT).each do |i|
    config.vm.define "tomcat#{i}" do |tomcat|
      tomcat.vm.hostname = "tomcat#{i}"
      tomcat.vm.network "private_network", ip: "192.168.10.#{i + 10}"
#      tomcat{i}.vm.network "forwarded_port", guest: 8080, host: 1800{i}
      tomcat.vm.provision "shell", inline: <<-SHELL
        yum -y install mc
        yum -y install java-1.8.0-openjdk
        yum -y install tomcat tomcat-webapps tomcat-admin-webapps
        systemctl enable tomcat
        systemctl start tomcat
        mkdir /usr/share/tomcat/webapps/pacavaca/
        echo "#{i}#{i}#{i}#{i}#{i}#{i}#{i}#{i}" >> /usr/share/tomcat/webapps/pacavaca/index.html
        echo "*********************************"
        echo "192.168.10.10    apache" >> /etc/hosts
        SHELL
    end
  end
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
    config.vm.box = "bento/centos-7.5"
  
  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
    config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   yum install mc -y
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end  
