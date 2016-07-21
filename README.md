## Deploy Rails

Contains necessary playbook to get started with ansible for basic rails apps

### Requirements

Checkout this project to your rails application parent folder and ensure this application and your rails application exists at same level

```sh
.
├── your_rails_application
└── deploy-rails

2 directories, 0 files
```

### Rails

Create the following task to package the rails app

```ruby
require 'rake/packagetask'

task :delete_pkg do
  FileUtils.rm_rf('pkg')
end

Rake::PackageTask.new('{{app_name}}', Rails.env) do |p|
  Rake::Task['delete_pkg'].invoke

  p.need_tar = true
  p.package_files = FileList['*', '**/*']
  p.package_files.exclude('.git', 'pkg/*', '.project', '.settings', '.gitignore', 'data/store/*',
                          'docs', 'docs/*', 'tmp')
end
```

> Replace app_name with your desired application name
 

### Ansible

#### install playbook from galaxy

```bash
ansible-galaxy install -r requirements.yml
```

#### create vault file

create a vault file `vault_pass.txt` with content as your password

```txt
password
```

#### config files

create config files for every environment and place it under `config` folder

```bash
config
├── ci.yml
├── development.yml
├── production.yml
├── qa.yml
└── uat.yml

0 directories, 5 files
```
> Example

```yaml
---
  rails_app_name: basic-app
  rails_app_root: /var/www
  rails_env: development
  rails_secret: 1fdb60e4fd34fe66289bdcaa2fb0f5e7fa8f4f52e21e48db8f49d640db6da02213d4fff25cbcc5f0d57589925fc5a67012cd4586a1ba332e81178b37fb22a36a
  database_host: 10.1.10.2
  database_name: basic_app
  database_username: basic
  database_password: password
  database_trusted_hosts:
    - 10.1.10.3/32
```

#### encrypt

Ensure to encrypt the config files with vault before pushing it to git.

```bash
ansible-vault encrypt --vault-password-file vault_pass.txt config/*.yml
```

### Vagrant

Ensure you have installed latest version of vagrant by executing:

```bash
vagrant --version
```

### Provisioning

Now, execute `vagrant up --provision` to begin provisioning the rails app.
