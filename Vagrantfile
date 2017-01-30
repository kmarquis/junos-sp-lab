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

# Provder Edge 1
  config.vm.define "pe1" do |pe1|
    pe1.vm.network "private_network", virtualbox__intnet: "net1" # PE1 - P1
    pe1.vm.network "private_network", virtualbox__intnet: "net2" # PE1 - PE2
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
    pe2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      pe2.vm.provider :virtualbox do |vb|
        vb.name = "PE2"
        vb.memory = 1024
        vb.gui = false
      end
  end

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
    p2.vm.network "private_network", virtualbox__intnet: "net2" # P2 - PE2
    p2.vm.network "private_network", virtualbox__intnet: "net7" # P2 - PE4
    p2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      p2.vm.provider :virtualbox do |vb|
        vb.name = "P2"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Edge 3
  config.vm.define "pe3" do |pe3|
    pe3.vm.network "private_network", virtualbox__intnet: "net6" # PE3 - P1
    pe3.vm.network "private_network", virtualbox__intnet: "net8" # PE3 - PE4
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
    rr1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      rr1.vm.provider :virtualbox do |vb|
        vb.name = "RR1"
        vb.memory = 512
        vb.gui = false
      end
  end  

# This is where the Ansible Groups and Playbook will be defined so when the
# the topology is built configuration will be automated and each vSRX will 
# havre the correct base confirguration

  config.vm.provision "ansible" do |ansible|
    ansible.groups = {
      "base" => ["p1", "p2", "pe1", "pe2", "pe3", "pe4", "rr1"],
      "p_routers" => ["p1", "p2"],
      "pe_routers" => ["pe1", "pe2", "pe3", "pe4"],
      "rr_router" => ["rr1"]
    }
    ansible.playbook = ansible.playbook = "junos-sp.yml"
    ansible.playbook = -vvv
  end

end
