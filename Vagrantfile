# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  
  config.vm.box = "ubuntu/trusty64"
  # config.vm.box_check_update = false
  config.vm.network "forwarded_port", guest: 80, host: 8080
  # config.vm.network "private_network", ip: "192.168.33.10"

  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  config.vm.provision "shell", inline: <<-SHELL
     echo 'Installing Apache2, MySQL (password: none)'
     export DEBIAN_FRONTEND=noninteractive
     debconf-set-selections <<< 'mysql-server mysql-server/root_password password none'
     debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password none'
     sudo apt-get update
     sudo apt-get dist-upgrade
     sudo apt-get install -q -y apache2 php5-ldap libapache2-mod-php5 tmux vim git shelldap mysql-server mc htop php5-json php-gd php5-mcrypt php5-xdebug php5-mysql

     # xdebug

     cat << EOF | sudo tee -a /etc/php5/mods-available/xdebug.ini
     xdebug.scream=1
     xdebug.cli_color=1
     xdebug.show_local_vars=1
     EOF

     # apache

     sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php5/apache2/php.ini
     sed -i "s/display_errors = .*/display_errors = On/" /etc/php5/apache2/php.ini
     sed -i "s/upload_max_filesize = .*/upload_max_filesize = 20M/" /etc/php5/apache2/php.ini
     sed -i "s/max_execution_time = .*/max_execution_time = 300/" /etc/php5/apache2/php.ini
     echo max_input_vars = 3000 >> /etc/php5/apache2/php.ini
     sed -i "s/post_max_size = .*/post_max_size = 20M/" /etc/php5/apache2/php.ini

     # typo3

     cd /tmp
     echo 'Downloading typo'
     wget -c "http://downloads.sourceforge.net/project/typo3/TYPO3%20Source%20and%20Dummy/TYPO3%207.4.0/typo3_src-7.4.0.tar.gz?r=http%3A%2F%2Ftypo3.org%2Fdownload%2F&ts=1442816204&use_mirror=netix" -O typo.tar.gz -q
     echo 'Installing typo3'
     cd /var/www/html
     tar xf /tmp/typo.tar.gz
     ln -s typo3_src-7.4.0 typo3_src
     ln -s typo3_src/index.php index.php
     ln -s typo3_src/typo3 typo3
     cp typo3_src/_.htaccess .htaccess
     touch FIRST_INSTALL
     chown -R vagrant:vagrant /var/www
     chmod -R a+rw /var/www
     echo 'http://localhost:8080/typo'
  SHELL
end
