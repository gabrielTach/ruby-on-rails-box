# OS detection
# Taken from https://stackoverflow.com/questions/26811089/vagrant-how-to-have-host-platform-specific-provisioning-steps
module OS
		def OS.windows?
				(/cygwin|mswin|mingw|bccwin|wince|emx/ =~ RUBY_PLATFORM) != nil
		end

		def OS.mac?
				(/darwin/ =~ RUBY_PLATFORM) != nil
		end

		def OS.unix?
				!OS.windows?
		end

		def OS.linux?
				OS.unix? and not OS.mac?
		end
end

# Vagrant configuration
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Ubuntu 2020.04
  config.vm.box = "ubuntu/focal64"

  config.vm.provider "virtualbox" do |vb|
    vb.customize [ "modifyvm", :id, "--uart1", "0x3F8", "4" ]
    vb.customize [ "modifyvm", :id, "--uartmode1", "file", File::NULL ]
		# Less than 1 GB and sassc will not compile
		vb.memory = "2048"
  end

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # Do not forget to run rails server -b 0.0.0.0
  config.vm.network "forwarded_port", guest: 3000, host: 3000

	# Forward inkbuckets' forwarded_port
	config.vm.network "forwarded_port", guest: 9000, host: 9000

	# For the sync folder use SMB on windows, as it will allow the creation of symlinks
	if OS.windows? then
		config.vm.synced_folder './vagrant', '/vagrant', type: 'smb', mount_options: ["mfsymlinks,dir_mode=0775,file_mode=0664"]
	else
		# Fsnotify will forward filesystem change notifications to the VM
		# Thus there will be no more need to modify config files in rails
		# https://github.com/adrienkohlbecker/vagrant-fsnotify
		config.vagrant.plugins = ["vagrant-fsnotify"]

		config.vm.synced_folder "./vagrant", "/vagrant", fsnotify: true

		# Run vagrant fsnotify everytime the box is up
		config.trigger.after :up do |t|
	    t.name = "vagrant-fsnotify"
	    t.run = { inline: "vagrant fsnotify" }
	  end
	end

  config.vm.provision "shell", inline: <<-'SHELL'
    # Yarn depository
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

    sudo apt-get update

    # Install all databases and other servers
    sudo apt-get install -y mysql-server libmysqlclient-dev libssl-dev
    sudo apt-get install -y postgresql  libpq-dev
    sudo apt-get install -y sqlite3 libsqlite3-dev
    sudo apt-get install -y memcached

		# Add vagrant role
		sudo -u postgres createuser vagrant -s && sudo -u postgres createdb vagrant

    # We need git and curl
    sudo apt-get install -y git curl gnupg2

		# Install mail catcher
		wget -nv -P /tmp/ -O inbucket.deb https://github.com/inbucket/inbucket/releases/download/v2.1.0/inbucket_2.1.0_linux_amd64.deb
		sudo dpkg -i /tmp/inbucket.deb
		sudo apt install -f
		sudo systemctl enable inbucket
		sudo systemctl start inbucket


    gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
    curl -sSL https://get.rvm.io | bash -s stable --rails

    # Install Ruby
    source /usr/local/rvm/scripts/rvm
  	sudo usermod -a -G rvm vagrant
    gem install

    # Install Yarn
    sudo apt-get -y  install yarn

    # Install Bundler
    gem install bundler

    # Install rails
    gem install rails
  SHELL
end
