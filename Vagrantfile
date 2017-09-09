# -*- mode: ruby -*-
# vi: set ft=ruby :

# Check required plugins
REQUIRED_PLUGINS = %w(vagrant-junos vagrant-host-shell)
exit unless REQUIRED_PLUGINS.all? do |plugin|
  Vagrant.has_plugin?(plugin) || (
    puts "The #{plugin} plugin is required. Please install it with:"
    puts "$ vagrant plugin install #{plugin}"
    false
  )
end

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

# This section is where the topology is defined. Vagrant will define what
# the topology will look like. The file give instructions to VirtualBox
# on how all the vSRXs are connected. This is how the physical environment 
# will be set up. Additionally resources are allocated to each VM 

# Provder Router 1
  config.vm.define "p1" do |p1|
    p1.vm.network "private_network", virtualbox__intnet: "net1" # P1 - PE1
    p1.vm.network "private_network", virtualbox__intnet: "net4" # P1 - RR1
    p1.vm.network "private_network", virtualbox__intnet: "net5" # P1 - P2
    p1.vm.network "private_network", virtualbox__intnet: "net6" # P1 - PE3
    p1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      p1.vm.provider :virtualbox do |vb|
        vb.name = "P1"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Router 2
  config.vm.define "p2" do |p2|
    p2.vm.network "private_network", virtualbox__intnet: "net5" # P2 - P1
    p2.vm.network "private_network", virtualbox__intnet: "net3" # P2 - PE2
    p2.vm.network "private_network", virtualbox__intnet: "net7" # P2 - PE4
    p2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      p2.vm.provider :virtualbox do |vb|
        vb.name = "P2"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Edge 1
  config.vm.define "pe1" do |pe1|
    pe1.vm.network "private_network", virtualbox__intnet: "net1" # PE1 - P1
    pe1.vm.network "private_network", virtualbox__intnet: "net2" # PE1 - PE2
    pe1.vm.network "private_network", virtualbox__intnet: "net9" # PE1 - CE1   
    pe1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      pe1.vm.provider :virtualbox do |vb|
        vb.name = "PE1"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Edge 2
  config.vm.define "pe2" do |pe2|
    pe2.vm.network "private_network", virtualbox__intnet: "net3" # PE2 - P2
    pe2.vm.network "private_network", virtualbox__intnet: "net2" # PE2 - PE1
    pe2.vm.network "private_network", virtualbox__intnet: "net12" # PE2 - CE2
    pe2.vm.network "private_network", virtualbox__intnet: "net13" # PE2 - CE3 
    pe2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      pe2.vm.provider :virtualbox do |vb|
        vb.name = "PE2"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Edge 3
  config.vm.define "pe3" do |pe3|
    pe3.vm.network "private_network", virtualbox__intnet: "net6" # PE3 - P1
    pe3.vm.network "private_network", virtualbox__intnet: "net8" # PE3 - PE4
    pe3.vm.network "private_network", virtualbox__intnet: "net17" # PE3 - CE4
    pe3.vm.network "private_network", virtualbox__intnet: "net18" # PE3 - CE5
    pe3.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      pe3.vm.provider :virtualbox do |vb|
        vb.name = "PE3"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Edge 4
  config.vm.define "pe4" do |pe4|
    pe4.vm.network "private_network", virtualbox__intnet: "net7" # PE4 - P2
    pe4.vm.network "private_network", virtualbox__intnet: "net8" # PE4 - PE3
    pe4.vm.network "private_network", virtualbox__intnet: "net21" # PE4 - CE6
    pe4.vm.network "private_network", virtualbox__intnet: "net22" # PE4 - CE7
    pe4.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      pe4.vm.provider :virtualbox do |vb|
        vb.name = "PE4"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Route Reflector 1
  config.vm.define "rr1" do |rr1|
    rr1.vm.network "private_network", virtualbox__intnet: "net4" # RR1 - P1
    rr1.vm.network "private_network", virtualbox__intnet: "net28" # RR1 - Ansible
    rr1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      rr1.vm.provider :virtualbox do |vb|
        vb.name = "RR1"
        vb.memory = 512
        vb.gui = false
      end
  end

# Ansible / Naplam Host
    config.vm.define "ansible" do |ansible|
        ansible.vm.box = "ubuntu/xenial64"
        ansible.vm.hostname = "ansible"
        ansible.vm.network 'private_network', ip: "192.168.1.20", virtualbox__intnet: "net28" # Ansible - RR1
        ansible.ssh.insert_key = true
          ansible.vm.provider :virtualbox do |vb|
            vb.name = "ansible"
            vb.memory = 512
            vb.gui = false
          end
        ansible.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.1.1 dev enp0s8
            sudo apt-get update && sudo apt-get upgrade -y
            sudo apt-get install build-essential libssl-dev libffi-dev python-dev libxml2-dev libxslt-dev python-pip python-dev -y
            sudo pip install --upgrade pip
            sudo pip install setuptools --upgrade
            sudo pip install junos-eznc napalm ansible napalm-ansible
          SHELL
        end   

# Customer Edge 1
  config.vm.define "ce1" do |ce1|
    ce1.vm.network "private_network", virtualbox__intnet: "net9" # CE1 - PE2
    ce1.vm.network "private_network", virtualbox__intnet: "net10" # CE1 - EH1
    ce1.vm.network "private_network", virtualbox__intnet: "net11" # CE1 - EH2
    ce1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce1.vm.provider :virtualbox do |vb|
        vb.name = "CE1"
        vb.memory = 512
        vb.gui = false
      end
  end


# Customer Edge 2
  config.vm.define "ce2" do |ce2|
    ce2.vm.network "private_network", virtualbox__intnet: "net12" # CE2 - PE2
    ce2.vm.network "private_network", virtualbox__intnet: "net14" # CE2 - EH3
    ce2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce2.vm.provider :virtualbox do |vb|
        vb.name = "CE2"
        vb.memory = 512
        vb.gui = false
      end
  end

# Customer Edge 3
  config.vm.define "ce3" do |ce3|
    ce3.vm.network "private_network", virtualbox__intnet: "net13" # CE3 - PE2
    ce3.vm.network "private_network", virtualbox__intnet: "net15" # CE3 - CE4
    ce3.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce3.vm.provider :virtualbox do |vb|
        vb.name = "CE3"
        vb.memory = 512
        vb.gui = false
      end
  end


# Customer Edge 4
  config.vm.define "ce4" do |ce4|
    ce4.vm.network "private_network", virtualbox__intnet: "net17" # CE4 - PE4
    ce4.vm.network "private_network", virtualbox__intnet: "net18" # CE4 - EH5
    ce4.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce4.vm.provider :virtualbox do |vb|
        vb.name = "CE4"
        vb.memory = 512
        vb.gui = false
      end
  end

# Customer Edge 5
  config.vm.define "ce5" do |ce5|
    ce5.vm.network "private_network", virtualbox__intnet: "net16" # CE5 - PE4
    ce5.vm.network "private_network", virtualbox__intnet: "net19" # CE5 - EH6
    ce5.vm.network "private_network", virtualbox__intnet: "net20" # CE5 - EH7
    ce5.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce5.vm.provider :virtualbox do |vb|
        vb.name = "CE5"
        vb.memory = 512
        vb.gui = false
      end
  end


# Customer Edge 6
  config.vm.define "ce6" do |ce6|
    ce6.vm.network "private_network", virtualbox__intnet: "net21" # CE6 - PE3
    ce6.vm.network "private_network", virtualbox__intnet: "net23" # CE6 - EH8
    ce6.vm.network "private_network", virtualbox__intnet: "net24" # CE6 - EH9
    ce6.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce6.vm.provider :virtualbox do |vb|
        vb.name = "CE6"
        vb.memory = 512
        vb.gui = false
      end
  end

# Customer Edge 7
  config.vm.define "ce7" do |ce7|
    ce7.vm.network "private_network", virtualbox__intnet: "net22" # CE7 - PE3
    ce7.vm.network "private_network", virtualbox__intnet: "net25" # CE7 - EH10
    ce7.vm.network "private_network", virtualbox__intnet: "net26" # CE7 - EH11
    ce7.vm.network "private_network", virtualbox__intnet: "net27" # CE7 - EH12
    ce7.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce7.vm.provider :virtualbox do |vb|
        vb.name = "CE7"
        vb.memory = 512
        vb.gui = false
      end
  end


# Server 1 
    config.vm.define "eh1" do |eh1|
        eh1.vm.box = "ubuntu/trusty64"
        eh1.vm.hostname = "server1"
        eh1.vm.network 'private_network', ip: "192.168.10.20", :netmask => "255.255.255.128", virtualbox__intnet: "net10" # EH1 - CE1
        eh1.ssh.insert_key = true
          eh1.vm.provider :virtualbox do |vb|
            vb.name = "eh1"
            vb.memory = 512
            vb.gui = false
          end
        eh1.vm.provision "shell", inline: <<-SHELL
	          sudo ip route add 192.168.0.0/16 via 192.168.10.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 2  
    config.vm.define "eh2" do |eh2|
        eh2.vm.box = "ubuntu/trusty64"
        eh2.vm.hostname = "eh2"
        eh2.vm.network 'private_network', ip: "192.168.10.140", :netmask => "255.255.255.128",virtualbox__intnet: "net11" # EH2 - CE1
        eh2.ssh.insert_key = true
          eh2.vm.provider :virtualbox do |vb|
            vb.name = "eh2"
            vb.memory = 512
            vb.gui = false
          end
        eh2.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.10.129 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 3 
    config.vm.define "eh3" do |eh3|
        eh3.vm.box = "ubuntu/trusty64"
        eh3.vm.hostname = "eh3"
        eh3.vm.network 'private_network', ip: "192.168.20.20", virtualbox__intnet: "net14" # EH3 - CE2
        eh3.ssh.insert_key = true
          eh3.vm.provider :virtualbox do |vb|
            vb.name = "eh3"
            vb.memory = 512
            vb.gui = false
          end
        eh3.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.20.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 4  
    config.vm.define "eh4" do |eh4|
        eh4.vm.box = "ubuntu/trusty64"
        eh4.vm.hostname = "eh4"
        eh4.vm.network 'private_network', ip: "192.168.30.20", virtualbox__intnet: "net15" # EH4 - CE3
        eh4.ssh.insert_key = true
          eh4.vm.provider :virtualbox do |vb|
            vb.name = "eh4"
            vb.memory = 512
            vb.gui = false
          end
        eh4.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.30.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 5 
    config.vm.define "eh5" do |eh5|
        eh5.vm.box = "ubuntu/trusty64"
        eh5.vm.hostname = "eh5"
        eh5.vm.network 'private_network', ip: "192.168.40.20", virtualbox__intnet: "net18" # EH5 - CE4
        eh5.ssh.insert_key = true
          eh5.vm.provider :virtualbox do |vb|
            vb.name = "eh5"
            vb.memory = 512
            vb.gui = false
          end
        eh5.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.40.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 6  
    config.vm.define "eh6" do |eh6|
        eh6.vm.box = "ubuntu/trusty64"
        eh6.vm.hostname = "eh6"
        eh6.vm.network 'private_network', ip: "192.168.50.20", :netmask => "255.255.255.128", virtualbox__intnet: "net19" # EH6 - CE5
        eh6.ssh.insert_key = true
          eh6.vm.provider :virtualbox do |vb|
            vb.name = "eh6"
            vb.memory = 512
            vb.gui = false
          end
        eh6.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.50.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 7 
    config.vm.define "eh7" do |eh7|
        eh7.vm.box = "ubuntu/trusty64"
        eh7.vm.hostname = "eh7"
        eh7.vm.network 'private_network', ip: "192.168.50.140", :netmask => "255.255.255.128", virtualbox__intnet: "net20" # EH7 - CE5
        eh7.ssh.insert_key = true
          eh7.vm.provider :virtualbox do |vb|
            vb.name = "eh7"
            vb.memory = 512
            vb.gui = false
          end
        eh7.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.50.129 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 8  
    config.vm.define "eh8" do |eh8|
        eh8.vm.box = "ubuntu/trusty64"
        eh8.vm.hostname = "eh8"
        eh8.vm.network 'private_network', ip: "192.168.60.20", :netmask => "255.255.255.128", virtualbox__intnet: "net23" # EH8 - CE6
        eh8.ssh.insert_key = true
          eh8.vm.provider :virtualbox do |vb|
            vb.name = "eh8"
            vb.memory = 512
            vb.gui = false
          end
        eh8.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.60.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 9 
    config.vm.define "eh9" do |eh9|
        eh9.vm.box = "ubuntu/trusty64"
        eh9.vm.hostname = "eh9"
        eh9.vm.network 'private_network', ip: "192.168.60.140", :netmask => "255.255.255.128", virtualbox__intnet: "net24" # EH9 - CE6
        eh9.ssh.insert_key = true
          eh9.vm.provider :virtualbox do |vb|
            vb.name = "eh9"
            vb.memory = 512
            vb.gui = false
          end
        eh9.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.60.129 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 10  
    config.vm.define "eh10" do |eh10|
        eh10.vm.box = "ubuntu/trusty64"
        eh10.vm.hostname = "eh10"
        eh10.vm.network 'private_network', ip: "192.168.70.20", :netmask => "255.255.255.192", virtualbox__intnet: "net25" # E10 - CE7
        eh10.ssh.insert_key = true
          eh10.vm.provider :virtualbox do |vb|
            vb.name = "eh10"
            vb.memory = 512
            vb.gui = false
          end
        eh10.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.70.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 11 
    config.vm.define "eh11" do |eh11|
        eh11.vm.box = "ubuntu/trusty64"
        eh11.vm.hostname = "eh11"
        eh11.vm.network 'private_network', ip: "192.168.70.120", :netmask => "255.255.255.192", virtualbox__intnet: "net26" # E11 - CE7
        eh11.ssh.insert_key = true
          eh11.vm.provider :virtualbox do |vb|
            vb.name = "eh11"
            vb.memory = 512
            vb.gui = false
          end
        eh11.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.70.65 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 12  
    config.vm.define "eh12" do |eh12|
        eh12.vm.box = "ubuntu/trusty64"
        eh12.vm.hostname = "eh12"
        eh12.vm.network 'private_network', ip: "192.168.70.160", :netmask => "255.255.255.192", virtualbox__intnet: "net27" # E12 - CE7
        eh12.ssh.insert_key = true
          eh12.vm.provider :virtualbox do |vb|
            vb.name = "eh12"
            vb.memory = 512
            vb.gui = false
          end
        eh12.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.70.129 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# This is where the Ansible Groups and Playbook will be defined so when the
# the topology is built configuration will be automated and each vSRX will 
# havre the correct base confirguration

  config.vm.provision "ansible" do |ansible|
    ansible.groups = {
      "base" => [
	"p1", 
	"p2", 
	"pe1", 
	"pe2", 
	"pe3", 
	"pe4", 
	"rr1"
	],
      "p_routers" => [
	"p1", 
	"p2"
	],
      "pe_routers" => [
	"pe1", 
	"pe2", 
	"pe3", 
	"pe4"
	],
      "rr_router" => [
	"rr1"
	],
      "ce_routers" => [
  "ce1",
  "ce2",
  "ce3",
  "ce4",
  "ce5",
  "ce6",
  "ce7"
        ]
    }
    ansible.playbook = "junos-sp.yml"
#    ansible.verbose = "vvv"
  end
end
