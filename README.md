# ruby-on-rails-box
Vagrant box for Ruby On Rails development. This vagrant container is for learning
how to develop in ruby and Ruby on Rails.

# What's in the box?
* Ruby
* Ruby on Rails
* MySQL
* sqlite3
* memcached

## How to use it
```
git clone https://github.com/gabrielTach/ruby-on-rails-box.git
cd ./ruby-on-rails-box/
vagrant up
```

This will clone the repository for you and then run vagrant. Vagrant will setup
the virtual server and then install for you ruby on rails and common web
development tools.

## Run a rails server
First get access to the server's terminal
```
vagrant ssh
```

And then, get to your project that is in the synced folder `vagrant/` (host).
```
cd vagrant/<your_project>
rails s -b 0.0.0.0
```
