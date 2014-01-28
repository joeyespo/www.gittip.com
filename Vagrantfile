# -*- mode: ruby -*-
# vi: set ft=ruby :

# Versions
UBUNTU_VERSION = 'lucid64'
POSTGRES_VERSION = '9.3.2'

# Project directory. This won't change.
PROJECT_DIRECTORY = 'www.gittip.com'

Vagrant.configure('2') do |config|
  config.vm.box = 'lucid64'
  config.vm.box_url = 'http://files.vagrantup.com/lucid64.box'

  # Sync the project directory and expose the app
  config.vm.synced_folder '.', "/home/vagrant/#{PROJECT_DIRECTORY}"
  config.vm.network :forwarded_port, guest: 8537, host: 8537

  # TODO: Pin apt-get packages to the same versions Heroku uses

  # Install Apt Repository for Postgres on Lucid
  config.vm.provision :shell, :inline => "sudo sh -c 'echo \"deb http://apt.postgresql.org/pub/repos/apt/ lucid-pgdg main\" > /etc/apt/sources.list.d/pgdg.list'"
  config.vm.provision :shell, :inline => "wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -"

  # Install dependencies
  config.vm.provision :shell, :inline => "sudo apt-get update"
  config.vm.provision :shell, :inline => "sudo apt-get -y install make git build-essential python-software-properties postgresql-#{POSTGRES_VERSION} postgresql-contrib-#{POSTGRES_VERSION} libpq-dev python-dev"

  # Configure Postgres
  config.vm.provision :shell, :inline => "sudo su - postgres -c 'psql -U postgres -qf /home/vagrant/#{PROJECT_DIRECTORY}/create_db.sql'"

  # Warn if Windows newlines are detected and try to fix the problem
  config.vm.provision :shell, :inline => <<-eos
    cd #{PROJECT_DIRECTORY}

    if egrep -ql $'\r'\$ README.md; then
      echo
      echo '*** WARNING ***'
      echo 'CRLF detected. You must fix the line endings manually before continuing.'
      echo
      echo 'PROBLEM'
      echo 'Vagrant syncs your working directory with Ubuntu. Scripts and the Makefile'
      echo 'expect Unix line endings, but git converts them to Windows CRLF with autocrlf.'
      echo
      echo 'SOLUTION'
      echo '1. git stash               # Stash your work'
      echo '2. git rm --cached -r .    # Remove everything from the index'
      echo '3. git reset --hard        # Remove the index and working directory from git'
      echo
      echo 'Run "vagrant up" again afterward to continue.'
      echo '***************'

      echo
      echo 'Running "git config core.autocrlf false"'
      git config core.autocrlf false

      exit 1
    fi
  eos

  # Set up the environment, the database, and run Gittip
  config.vm.provision :shell, :inline => "cd #{PROJECT_DIRECTORY} && make local.env env schema data"

  # Add run script
  config.vm.provision :shell, :inline => <<-eos
    echo "#!/bin/sh" > run
    echo "sudo pkill aspen" >> run
    echo "cd #{PROJECT_DIRECTORY}" >> run
    echo "make run" >> run
    chmod +x run

    echo
    echo 'Gittip installed! To run,'
    echo '$ vagrant ssh'
    echo '$ ./run'
  eos
end
