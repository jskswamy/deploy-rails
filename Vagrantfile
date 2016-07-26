Vagrant.configure(2) do |config|

  machines = [
    {
      :name => "database.example.com",
      :ipaddress => "10.1.10.2",
      :memory => "512",
      :box => "ubuntu/trusty64",
      :ports => [],
      :shared_folders => []
    },
    {
      :name => "app-server.example.com",
      :ipaddress => "10.1.10.3",
      :memory => "1024",
      :box => "ubuntu/trusty64",
      :ports => [],
      :shared_folders => [
        {:dest => '/opt/shared/app', :src => '../basic-app'}
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

      machine[:shared_folders].each do |shared_folder|
        config.vm.synced_folder shared_folder[:src], shared_folder[:dest]
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
    ansible.raw_arguments = "--vault-password-file=vault_pass.txt"
    ansible.extra_vars = {
      env: 'development'
    }
    ansible.groups = {
      "database" => ["database.example.com"],
      "app-server" => ["app-server.example.com"]
    }
  end

end
