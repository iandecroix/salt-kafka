# vi: set ft=ruby :
MASTER_IP = '192.168.56.140'
# NOTE - building from git can take a lot of time and contain bugs
SALT = 'stable' # stable|git|daily|testing
# version to check out if using git
SALT_VERSION = "v2016.11.2"

boxes = [
  {
    :name       => "k-xenial",
    :mem        => "2048",
    :cpu        => "2",
    :ip         => "192.168.56.144",
    :image      => 'ubuntu/xenial64',
    :saltmaster => false
  },
  {
    :name       => "k-jessie",
    :mem        => "2048",
    :cpu        => "2",
    :ip         => "192.168.56.145",
    :image      => 'debian/jessie64',
    :saltmaster => false
  },
  {
    :name       => "k-stretch",
    :mem        => "2048",
    :cpu        => "2",
    :ip         => "192.168.56.146",
    :image      => 'debian/stretch64',
    :saltmaster => false
  },
  {
    :name       => "saltmaster",
    :mem        => "512",
    :cpu        => "2",
    :ip         => MASTER_IP,
    :image      => "ubuntu/xenial64",
    :saltmaster => true
  }
]

Vagrant.configure(2) do |config|
  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.box = opts[:image]
      config.vm.hostname = opts[:name]
      config.vm.network 'private_network',
        ip: opts[:ip]
      config.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", opts[:mem]]
        v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
      end
      config.vm.provision "shell",
        inline: "grep salt /etc/hosts || sudo echo \"#{MASTER_IP}\"  salt >> /etc/hosts"
      config.vm.provision :salt do |salt|
        salt.minion_config = "vagrant/config/minion"
        salt.masterless = false
        salt.run_highstate = false
        salt.install_type = SALT
        salt.install_master = opts[:saltmaster]
        if opts[:saltmaster] == true
          salt.master_config = "vagrant/config/master"
        end
      end
    end
  end
end
