# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "netsage-dev", primary: true, autostart: true do |netsage|
    # set box to official CentOS 7 image
    netsage.vm.box = "centos/7"
    # explcitly set shared folder to virtualbox type. If not set will choose rsync 
    # which is just a one-way share that is less useful in this context
    netsage.vm.synced_folder ".", "/vagrant", type: "virtualbox"
    # Set hostname
    netsage.vm.hostname = "netsage-dev"
    
    # Enable IPv4. Cannot be directly before or after line that sets IPv6 address. Looks
    # to be a strange bug where IPv6 and IPv4 mixed-up by vagrant otherwise and one 
    #interface will appear not to have an address. If you look at network-scripts file
    # you will see a mangled result where IPv4 is set for IPv6 or vice versa
    netsage.vm.network "private_network", ip: "10.3.3.3"
    
    #Disable selinux
    netsage.vm.provision "shell", inline: <<-SHELL
        sed -i s/SELINUX=enforcing/SELINUX=permissive/g /etc/selinux/config
    SHELL
    
    #reload VM since selinux requires reboot. Requires `vagrant plugin install vagrant-reload`
    netsage.vm.provision :reload

    #Install all requirements and perform initial setup
    netsage.vm.provision "shell", inline: <<-SHELL
        yum install -y epel-release
        yum clean all
        yum install -y gcc\
            kernel-devel\
            kernel-headers\
            dkms\
            make\
            bzip2\
            perl\
            perl-devel\
            net-tools\
            git\
            npm\
            https://dl.grafana.com/oss/release/grafana-6.2.5-1.x86_64.rpm 
        
        #Enable grafana
        systemctl enable grafana-server
        systemctl start grafana-server
        
        #Install wizzy
        npm install -g wizzy
        
        cd /vagrant
        ##
        # This is how the github repo was built
        #wizzy init
        #wizzy set grafana url https://portal.netsage.global/grafana/
        
        ##
        # Point at local grafana instance for easy deployment
        wizzy set grafana url http://localhost:3000
        wizzy set grafana username admin
        wizzy set grafana password admin
    SHELL
  end
end
