# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing
VAGRANTFILE_API_VERSION = '2'
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = 'ubuntu/xenial64'

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 9200, host: 9201
  config.vm.network "forwarded_port", guest: 5601, host: 5601
  config.vm.network "forwarded_port", guest: 5044, host: 5044

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

  config.vm.provider 'virtualbox' do |vb|
    vb.memory = '4096'
  end

  config.vm.provision 'file', source: 'config', destination: '/tmp'
  config.vm.provision 'shell', path: 'script.sh'

  config.vm.provision 'docker' do |d|
    d.pull_images 'elasticsearch'
    d.pull_images 'logstash'
    d.pull_images 'kibana'
    d.run 'elasticsearch', image: 'elasticsearch', args: '-d --restart always --name elastic01 -p 9200:9200'
    d.run 'logstash', image: 'logstash', args: '-d -it --name logstash01 -p 5044:5044 --link elastic01 -v /vagrant/config:/config'
    # docker run -d -it --name logstash01 -p 5044:5044 --link elastic01 -v /vagrant/config:/config logstash logstash -f /config/logstash.conf

    # d.run 'logstash', image: 'logstash', args: "-it --rm -v $PWD:/config-dir logstash -f /config-dir/logstash.conf"
    # docker run -it --rm -v "$PWD":/config-dir logstash -f /config-dir/logstash.conf

    # d.run 'kibana', image: 'kibana', args: '-d --name kibana01 -p 5601:5601 --link elastic01'
    d.run 'kibana', image: 'kibana', args: '-d --name kibana01 -p 5601:5601 -e ELASTICSEARCH_URL=http://localhost:9200'
    # docker run -p 5601:5601 --link elastic01 -d --name kibana01 -e ELASTICSEARCH_URL=http://localhost:9200
  end
end
