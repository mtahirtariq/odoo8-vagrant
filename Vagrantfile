# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty32"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 8069, host: 8069

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "private_network", type: "dhcp"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Change network card to PCnet-FAST III
    # For NAT adapter
    vb.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
    # For host-only adapter
    vb.customize ["modifyvm", :id, "--nictype2", "Am79C973"]
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    wget http://download.gna.org/wkhtmltopdf/0.12/0.12.3/wkhtmltox-0.12.3_linux-generic-i386.tar.xz
    tar xf wkhtmltox-0.12.3_linux-generic-i386.tar.xz
    sudo mv wkhtmltox/bin/wkhtmltopdf /usr/bin/
    sudo mv wkhtmltox/bin/wkhtmltoimage /usr/bin/
    rm -r wkhtmltox
    rm -r wkhtmltox-0.12.3_linux-generic-i386.tar.xz

    echo "deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
    wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    sudo apt-get update
    sudo apt-get install -y python-dev libpq-dev libxml2-dev libxslt1-dev libevent-dev libsasl2-dev libldap2-dev libjpeg-dev libfreetype6-dev zlib1g-dev
    sudo ln -s /usr/lib/`uname -i`-linux-gnu/libfreetype.so /usr/lib/
    sudo ln -s /usr/lib/`uname -i`-linux-gnu/libjpeg.so /usr/lib/
    sudo ln -s /usr/lib/`uname -i`-linux-gnu/libz.so /usr/lib/
    sudo apt-get install -y python-pip git
    sudo apt-get install -y postgresql
    sudo su - postgres -c "createuser -s vagrant"
    sudo apt-get install -y nodejs
    sudo ln -s /usr/bin/nodejs /usr/bin/node
    sudo apt-get install -y npm
    sudo npm install -g less less-plugin-clean-css

    git clone https://www.github.com/odoo/odoo --depth 1 --branch 8.0 --single-branch /opt/odoo/
    sudo mkdir /etc/odoo/
    sudo mkdir /var/log/odoo/
    sudo chown vagrant:vagrant -R /opt/odoo
    sudo chown vagrant:vagrant -R /etc/odoo
    sudo chown vagrant:vagrant -R /var/log/odoo
    sudo pip install -r /opt/odoo/requirements.txt

    mkdir /vagrant/myaddons/
    echo -e "[options]\nadmin_passwd = admin\ndb_host = False\ndb_port = False\ndb_user = vagrant\ndb_password = False\naddons_path = /opt/odoo/openerp/addons,/opt/odoo/addons,/vagrant/myaddons\nlogfile=/var/log/odoo/odoo-server.log" | tee /etc/odoo/openerp-server.conf
    sudo apt-get install -y supervisor
    echo -e "[program:odoo]\ncommand=/opt/odoo/odoo.py --config /etc/odoo/openerp-server.conf\ndirectory=/opt/odoo\nuser=vagrant\nautostart=true\nautorestart=true\nstdout_logfile=/var/log/supervisor/odoo.stdout.log\nstderr_logfile=/var/log/supervisor/odoo.stderr.log" | sudo tee /etc/supervisor/conf.d/odoo.conf
    sudo supervisorctl reread
    sudo supervisorctl update
  SHELL
end
