# ruby-on-rails-box
![](https://github.com/gabrielTach/ruby-on-rails-box/blob/master/.github/kookaburra%20-%20John%20Sherer.png?raw=true)

Vagrant box for Ruby On Rails development. This [vagrant](https://www.vagrantup.com/) container is for learning how to develop in ruby and Ruby on Rails.

## Requirements
* Vagrant 2.2.6 at least.
* Samba - file sharing (Windows) or rsync (GNU/Linux).

## What's in the box?
* Ruby
* Ruby on Rails
* [Inbucket](https://github.com/inbucket/inbucket)'s mail catcher.

### Databases
* MySQL
* sqlite3
* Postgresql

### Caching system
* memcached

### Mail catcher
* inbucket

## How to use it
```
git clone https://github.com/gabrielTach/ruby-on-rails-box.git
cd ./ruby-on-rails-box/
vagrant up
```

This will clone the repository for you and then run vagrant. Vagrant will setup the virtual server and then install for you ruby on rails and common web development tools.

The shared folder is `./vagrant` on the host machine and `/vagrant/` on the virtual machine.

### Run a rails server
First get access to the server's terminal
```
vagrant ssh
```

And then, get to your project that is in the synced folder `vagrant/` (host).
```
cd vagrant/<your_project>
rails s -b 0.0.0.0
```

## Notes
### Inbucket's mail catcher
To access to the mail catcher web interface, follow this url : [http://127.0.0.1:9000](http://127.0.0.1:9000).

To send an email with your rail application, add this in your __config/environments/development.rb__ file.

```
## Use inbucket's mail catcher
config.action_mailer.perform_caching = false
config.action_mailer.delivery_method = :smtp
config.action_mailer.perform_deliveries = true
config.action_mailer.raise_delivery_errors = true
config.action_mailer.smtp_settings = { :address => "localhost", :port => 2500 }
config.action_mailer.default_url_options = {:host => "localhost", :port => '3000', :protocol => "http"}
```

### Fsnotify plugin
This box uses the plugin [Fsnotify](https://github.com/adrienkohlbecker/vagrant-fsnotify) on systems which have rsync. This allows to make the Rails server aware of modifications on files. Be aware that there is a limitation, it only notifies the changes in a two seconds timeframe. You can read more details about it on the [GitHub project's page](https://github.com/adrienkohlbecker/vagrant-fsnotify).
