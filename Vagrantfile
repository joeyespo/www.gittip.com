# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

PROJECT_DIRECTORY = 'www.gittip.com'
DATABASE_URL = 'postgres://gittip:gittip@localhost:5432/gittip'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.synced_folder ".", "/home/vagrant/#{PROJECT_DIRECTORY}"
  config.vm.network :forwarded_port, guest: 8537, host: 8537

  # Install dependencies
  config.vm.provision :shell, :inline => "sudo apt-get update"
  config.vm.provision :shell, :inline => "sudo apt-get -y install make python-dev python-software-properties g++ git postgresql postgresql-contrib postgresql-server-dev-9.1"

  # Warn if Windows newlines are detected and try to fix the problem
  config.vm.provision :shell, :inline => <<-eos
    cd #{PROJECT_DIRECTORY}
    if egrep -ql $'\r'\$ README.md; then
        echo
        echo '*** WARNING ***'
        echo 'CRLF detected. You probably forgot to set autocrlf=false.'
        echo 'You must fix the line endings manually with "git reset" before running "vagrant up" again.'
        echo '***************'
        echo

        echo 'Running "git config core.autocrlf false"'
        git config core.autocrlf false

        exit 1
    fi
  eos

  # Create local environment
  config.vm.provision :shell, :inline => "cd #{PROJECT_DIRECTORY} && make local.env"
end
