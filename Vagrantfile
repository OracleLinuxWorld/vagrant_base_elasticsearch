Vagrant.configure("2") do |config|

  # Create a base box with OL7 and pre-install the latest Elastic 6.x components
  config.vm.define "es_basebox" do |es_basebox|
    es_basebox.vm.synced_folder "./vagrant", "/vagrant"
    es_basebox.vm.box = "ol75"
    es_basebox.vm.hostname = 'es-basebox'
    es_basebox.vm.box_url = "http://yum.oracle.com/boxes/oraclelinux/ol75/ol75.box"
    es_basebox.vm.network :"private_network", type: "dhcp"
    es_basebox.vm.network "forwarded_port", guest: 9200, host: 9200, protocol: "tcp"
    es_basebox.vm.network "forwarded_port", guest: 5601, host: 5601, protocol: "tcp"
    es_basebox.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--memory", 1024]
      v.customize ["modifyvm", :id, "--cpus", "2"]
      v.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
      v.customize ["modifyvm", :id, "--usb", "off"]
      v.customize ["modifyvm", :id, "--audio", "none"]
      v.customize ["modifyvm", :id, "--name", "es_basebox"]
    end # End of "es_basebox.vm.provider"
  end   # End of config.vm.define "es_basebox"


    # Set auto_update to false
    # This will not automatically update the guest additions on VM boot
    # Set to "true" if you want auto-updates
    # config.vbguest.auto_update = false

    # Run the same playbook on all hosts
    # :vars section provided as example on passing variables to
    # ansible in possible future versions
    config.vm.provision "ansible_local" do |ansible|
          ansible.verbose = "v"
          ansible.playbook = "ansible-playbook.yml"
          ansible.groups = {
            "elasticnodes" => ["es_basebox"],
            "elasticnodes:vars" => {"variable1" => "example1",
                                    "variable2" => "example2"}
          }
    end   # End of "config.vm.provision"

end       # End of "Vagrant.configure"
