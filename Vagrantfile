Vagrant.configure(2) do |config|

  machines = [
    {
      :name => "database.example.com",
      :ipaddress => "10.1.10.2",
      :memory => "512",
      :box => "ubuntu/trusty64",
      :ports => []
    },
    {
      :name => "app-server.example.com",
      :ipaddress => "10.1.10.3",
      :memory => "1024",
      :box => "ubuntu/trusty64",
      :ports => [
        {:guest => 3000, :host => 8084}
      ]
    },
  ]

  machines.each do |machine|
    config.vm.define machine[:name] do |instance|
      instance.vm.box = machine[:box]
      instance.vm.hostname = machine[:name]
      instance.vm.network "private_network", ip: machine[:ipaddress]
      machine[:ports].each do |port|
        instance.vm.network "forwarded_port", host: port[:host], guest: port[:guest]
      end

      instance.vm.provider "virtualbox" do |vb|
        vb.name = machine[:name]
        vb.memory = machine[:memory]
      end
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "app.yml"
    ansible.host_key_checking = false
    ansible.extra_vars = {
      rails_app_name: 'basic-app',
      rails_app_root: '/var/www',
      rails_env: 'development',
      database_trusted_hosts: ['10.1.10.3/32'],
      database_host: '10.1.10.2',
      database_name: 'basic_app',
      database_username: 'basic',
      database_password: 'password'
    }
    ansible.groups = {
      "database" => ["database.example.com"],
      "app-server" => ["app-server.example.com"]
    }
  end

end
