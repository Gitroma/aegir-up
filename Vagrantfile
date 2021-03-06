Vagrant::Config.run do |config|
  require "settings"

  # base box
  config.vm.box = Vm::Basebox
  config.vm.box_url = Vm::Box_url

  # add a gui
  if Vm::Gui == 1
    config.vm.boot_mode = :gui
  end

  # set memory to 512MB
  config.vm.customize do |vm|
    vm.memory_size = Vm::Memory
  end

  # add a hostmaster Aegir server on a shared host-only network
  config.vm.define Hm::Shortname do |hm_config|
    hm_config.vm.network("192.168.#{Vm::Subnet}.10")
    hm_config.vm.host_name = Hm::Hostname
    hm_config.vm.customize do |vm|
      vm.name = Hm::Vmname
      if defined?(Hm::Memory)
        vm.memory_size = Hm::Memory
      end
    end
    if File::exists?("#{Vm::Manifests}/#{Hm::Shortname}.pp")
      hm_config.vm.provision :puppet do |puppet|
        puppet.manifest_file = "#{Hm::Shortname}.pp"
        puppet.module_path   = Vm::Modules
        if defined?(Vm::Options)
          puppet.options       = Vm::Options
        end
      end
    end
  end

  # add several hostslave Aegir servers on a shared host-only network
  (1..Hs::Count).each do |index|
    config.vm.define "#{Hs:Shortname}#{index}" do |hs_config|
      hs_config.vm.network("192.168.#{Vm::Subnet}.1#{index}")
      hs_config.vm.host_name = "#{Hs::Hostname}#{index}"
      hs_config.vm.customize do |vm|
        vm.name = "#{Hs::Vmname} #{index}"
      if defined?(Hs::Memory)
        vm.memory_size = Hs::Memory
      end
    end
     if File::exists?("#{Vm::Manifests}/#{Hm::Shortname}.pp")
       hs_config.vm.provision :puppet do |puppet|
          puppet.module_path    = Vm::Modules
          puppet.manifest_file  = "#{Hs::Shortname}.pp"
          if defined?(Vm::Options)
            puppet.options       = Vm::Options
          end
        end
      end
    end
  end
end
