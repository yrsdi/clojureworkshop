# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_url = "https://atlas.hashicorp.com/ubuntu/boxes/trusty64"
  config.vm.box_check_update = false

  config.vm.network "forwarded_port", guest: 3000, host: 13000
  config.vm.network "forwarded_port", guest: 5432, host: 15432
  config.vm.network "forwarded_port", guest: 8888, host: 18888

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "2048"
  end
  config.vm.provision "shell", inline: <<-PROVISION
    echo "[1/5] Installing language-pack-fi..."
    sudo apt-get install language-pack-fi -y >> /var/log/clojure-box-provision.log 2>&1

    echo "[2/5] Installing Java8..."
    sudo echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | \
    sudo /usr/bin/debconf-set-selections >> /var/log/clojure-box-provision.log 2>&1
    sudo add-apt-repository ppa:webupd8team/java >> /var/log/clojure-box-provision.log 2>&1
    sudo apt-get update &> /dev/null
    sudo apt-get install oracle-java8-installer -y >> /var/log/clojure-box-provision.log 2>&1

    echo "[3/5] Installing Postgresql 9.3..."
    sudo apt-get install postgresql-9.3 -y >> /var/log/clojure-box-provision.log 2>&1
    echo "[4/5] Creating database shorturl user and database..."
    sudo -u postgres createuser vagrant -s
    sudo -u postgres createdb shorturl
    sudo -u postgres psql -f /vagrant/shorturl/db/create_user_and_table.postgresql shorturl &> /dev/null

    echo "[5/5] Leiningen 2..."
    sudo wget -q https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein -O /usr/local/bin/lein &> /dev/null
    sudo chmod 755 /usr/local/bin/lein
    su - vagrant -c 'lein &> /dev/null'

    echo "Box is ready!"
    echo "============="
    echo "To run and access the application do the following:"
    echo "$ vagrant ssh"
    echo "$ cd /vagrant/shorturl && lein ring server-headless"
    echo "$ Direct browser on host machine to: http://localhost:13000"
    echo " --- Happy coding ---"
  PROVISION
end
