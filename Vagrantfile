# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/centos-7.2"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 80, auto_correct:true
  config.vm.network "forwarded_port", guest: 443, host: 443, auto_correct:true
  config.vm.network "forwarded_port", guest: 8443, host: 8443, auto_correct:true
  config.vm.network "forwarded_port", guest: 8080, host: 8080, auto_correct:true


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
  config.vm.synced_folder "./data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
   config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
     vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "4096"
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
     yum update
     yum -y install java-1.8.0-openjdk
     update-alternatives --config java
     update-alternatives --config javac
     cd /opt
     wget http://mirror.idealhosting.net.tr/Apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
     tar xzf apache-maven-3.3.9-bin.tar.gz
     sudo ln -s apache-maven-3.3.9 maven
     echo "export M2_HOME=/opt/maven" >> /etc/profile.d/maven.sh
     echo "export PATH=${M2_HOME}/bin:${PATH}" >> /etc/profile.d/maven.sh
     source /etc/profile.d/maven.sh
     yum -y install git
     git clone -b git-clone-url-update https://github.com/7storm7/thingsboard.git 
     cd thingsboard     
     mvn clean install  

     touch /etc/yum.repos.d/datastax.repo 
     echo '[datastax]' | sudo tee --append /etc/yum.repos.d/datastax.repo > /dev/null
     echo 'name = DataStax Repo for Apache Cassandra' | sudo tee --append /etc/yum.repos.d/datastax.repo > /dev/null
     echo 'baseurl = http://rpm.datastax.com/community' | sudo tee --append /etc/yum.repos.d/datastax.repo > /dev/null
     echo 'enabled = 1' | sudo tee --append /etc/yum.repos.d/datastax.repo > /dev/null
     echo 'gpgcheck = 0' | sudo tee --append /etc/yum.repos.d/datastax.repo > /dev/null

     yum install -y dsc30
     # Tools installation
     yum install -y cassandra30-tools
     # Start Cassandra
     service cassandra start
     # Configure the database to start automatically when OS starts.
     chkconfig cassandra on

     rpm -Uvh /opt/thingsboard/application/target/thingsboard.rpm

     cqlsh -f /usr/share/thingsboard/data/schema.cql
     cqlsh -f /usr/share/thingsboard/data/system-data.cql
     cqlsh -f /usr/share/thingsboard/data/demo-data.cql
     
     
     service thingsboard start
   SHELL
end
