require 'yaml'
require 'fileutils'

config = {
  local: './vagrant/config/vagrant-local.yml',
  example: './vagrant/config/vagrant-local.example.yml'
}

# copy config from example if local config not exists
FileUtils.cp config[:example], config[:local] unless File.exist?(config[:local])
# read config
options = YAML.load_file config[:local]

# vagrant configurate
Vagrant.configure(2) do |config|
  # select the box
  config.vm.box = 'centos/7'

  # port forwarding
  config.vm.network :forwarded_port, host: options['port_host1'], guest: options['port_virtual1']
  config.vm.network :forwarded_port, host: options['port_host2'], guest: options['port_virtual2']
  # should we ask about box updates?
  config.vm.box_check_update = options['box_check_update']

  config.vm.provider 'virtualbox' do |vb|
    # machine cpus count
    vb.cpus = options['cpus']
    # machine memory size
    vb.memory = options['memory']
    # machine name (for VirtualBox UI)
    vb.name = options['machine_name']
  end

  # machine name (for vagrant console)
  config.vm.define options['machine_name']

  # machine name (for guest machine console)
  config.vm.hostname = options['machine_name']

  # network settings
  config.vm.network 'private_network', ip: options['ip']

  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "main.yml"
    ansible.install_mode = "pip"
    ansible.raw_arguments = ["-vv"]
  end
end