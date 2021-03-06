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

  # Resolve "stdin: is not a tty" errors
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  #config.vm.box = "hashicorp/precise32"
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = "webpagetest"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 8080

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

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end

  # Proxy configuration
  # config.proxy.http     = "http://your_proxy:8080"
  # config.proxy.https    = "http://your_proxy:8080"
  # config.proxy.no_proxy = "localhost,127.0.0.1"

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL

  # INSTALL
  #   Apache 2.x+ with the following modules enabled:
  #     mod_expires
  #     mod_headers
  #     mod_rewrite
  #   PHP 5.3.0 or later with the following modules:
  #     gd
  #     zip
  #     zlib
  #     curl (if you want to use remote-storage like S3 and Google storage)
  #     sqlite (if you want to be able to edit test labels)
  #   ffmpeg
  #     Make available in the path (used to extract video thumbnail images)
  #     For mobile agent video support it needs to be 1.x (Linux, Windows)
  #   imagemagick (optional)
  #     Necessary for mobile video processing if using the mobile Node.js agent
  #   jpegtran (optional)
  #     Make available in the path (used for displaying jpeg analysis)
  #   exiftool (optional)
  #     Make available in the path (used for displaying jpeg analysis)

  config.vm.provision "shell", inline: <<-SHELL
    # update box
    sudo apt-get update
    #sudo apt-get upgrade

    # install apache
    echo -e "Install apache2..."
    sudo apt-get install -y apache2
    # active apache modules
    sudo a2enmod expires
    sudo a2enmod headers
    sudo a2enmod rewrite
    # restart apache
    sudo service apache2 restart

    # install php & extensions
    echo -e "Install php..."
    sudo apt-get install -y php5 php5-gd php5-curl php5-sqlite

    # install ffmpeg
    echo -e "Install ffmpeg..."
    #sudo apt-get install -y ffmpeg
    # install "classical" codecs
    #sudo apt-get install -y libavcodec-unstripped-52 libavdevice-unstripped-52
    #sudo apt-get install -y libavcodec54 libavdevice53 libavformat54 libavutil52 libpostproc52 libswscale2 ffmpeg
    sudo add-apt-repository ppa:mc3man/trusty-media
    sudo apt-get update
    sudo apt-get install -y ffmpeg

    # install imagemagick
    echo -e "Install imagemagick..."
    sudo apt-get install -y imagemagick

    # install jpegtran
    echo -e "Install jpegtran..."
    #sudo apt-get install -y jpegtran
    sudo apt-get install -y libjpeg-progs

    # install exiftool
    echo -e "Install exiftool..."
    sudo apt-get install -y exiftool

    # install python with modules
    echo -e "Install pillow..."
    sudo apt-get install -y python2.7-dev python-pip
    sudo pip install pillow

    # download webpagetest (from github)
    echo -e "Install webpagetest..."
    cd /vagrant/data
    # git clone (solution #1)
    sudo apt-get install -y git
    sudo git clone https://github.com/WPO-Foundation/webpagetest.git
    # dwl archive (solution #2)
    #sudo apt-get install -y zip
    #sudo rm -f master.zip
    #sudo wget https://github.com/WPO-Foundation/webpagetest/archive/master.zip
    #sudo unzip -o -q master.zip -d webpagetest
    #sudo rm master.zip

    # init apache vhost
    echo -e "Setting virtual host..."
    sudo mkdir -p /var/www/webpagetest
    #sudo chmod -R 755 /var/www/
    # copy www content
    sudo cp -r /vagrant/data/webpagetest/www/* /var/www/webpagetest/
    sudo chown -R www-data:www-data /var/www/webpagetest
    # copy settings
    sudo cp -r -v /vagrant/data/settings/* /var/www/webpagetest/settings/
    # config vhost
    sudo cp /vagrant/data/vhost /etc/apache2/sites-available/webpagetest.conf
    # enable apache vhost
    sudo a2ensite webpagetest
    # disable all default vhost
    sudo a2dissite 000-default
    # reload apache
    sudo service apache2 reload

    echo -e "Your WebPageTest Private Instance is ready..."
    echo -e " =>  http://webpagetest:8080/"

  SHELL
end
