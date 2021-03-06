require 'vagrant-aws'
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
  # config.vm.box = 'ubuntu/xenial64'
  config.vm.box = 'aws-dummy'

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

  config.vm.provider 'aws' do |aws, override|
    # Read AWS authentication information from environment variables
    aws.access_key_id = ENV['AWS_ACCESS_KEY_ID']
    aws.secret_access_key = ENV['AWS_SECRET_ACCESS_KEY']

    # Specify SSH keypair to use
    aws.keypair_name = 'ssh-keypair-name'

    # Specify region, AMI ID, and security group(s)
    aws.region = 'us-west-2'
    aws.ami = 'ami-20be7540'
    aws.security_groups = ['default']

    # Specify username and private key path
    override.ssh.username = 'ubuntu'
    override.ssh.private_key_path = '~/.ssh/ssh-keypair-file'
  end

  config.vm.provision 'file', source: 'config', destination: '/tmp'
  config.vm.provision 'shell', path: 'script.sh'

  config.vm.provision 'docker' do |d|
    d.pull_images 'elasticsearch'
    d.pull_images 'logstash'
    d.pull_images 'kibana'
    d.run 'elasticsearch', image: 'elasticsearch', args: '-d --restart always --name elastic01 -p 9200:9200'
    d.run 'logstash', image: 'logstash', args: '-d -it --name logstash01 -p 5044:5044 --link elastic01:elasticsearch -v /vagrant/config:/config'
    d.run 'kibana', image: 'kibana', args: '-d --name kibana01 -p 5601:5601 --link elastic01:elasticsearch'
  end
end
