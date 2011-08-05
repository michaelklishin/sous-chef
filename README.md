# What is Travis Sous Chef

Travis Sous Chef is a repository that makes development and testing of Chef cookbooks (for travis-ci.org but
not just) easy.


## Dependencies

Sous Chef uses [Vagrant](http://vagrantup.com) and thus relies on [Virtual Box](http://virtualbox.org) being installed. Note that you do not


## Getting started with Sous Chef

First install [VirtualBox 4.0.x](http://download.virtualbox.org/virtualbox/4.0.12) (4.1.0 has known issues and we recommend holding off with upgrades):

* For [Mac OS X](http://download.virtualbox.org/virtualbox/4.0.12/VirtualBox-4.0.12-72916-OSX.dmg)
* For [64-bit Linux distributions](http://download.virtualbox.org/virtualbox/4.0.12/)

Then install the vagrant gem:

    gem install vagrant --version="~> 0.7.7" # 0.8.x only works with VirtualBox 4.1.x, we recommend not upgrading just yet

Check Vagrant version:

    vagrant --version # => Vagrant version 0.7.7

Copy sample Vagrant file:

    cp Vagrantfile.sample Vagrantfile

Create a 32-bit Ubuntu 10.04.3 virtual machine you will be developing cookbooks in:

    vagrant up 

Once VM is created, create cookbooks directory or clone an existing collection of cookbooks like [Travis CI cookbooks](https://github.com/travis-ci/travis-cookbooks):

    mkdir cookbooks
    # or
    git clone git://github.com/travis-ci/travis-cookbooks.git cookbooks

After that point Vagrant at the cookbooks location by editing Vagrantfile. For Travis CI cookbooks, you just need to uncomment

    # chef.cookbooks_path = "cookbooks/vagrant_base"

for other cookbook collections, provide a local path like so

    chef.cookbooks_path = "cookbooks"

Next choose some cookbooks to provision. In the case of Travis CI cookbooks, build-essential is a good one to start with, so uncomment

    # chef.add_recipe     "build-essential" 

Your Vagrantfile then will look like this:

    Vagrant::Config.run do |config|
      config.vm.box     = "base"
      config.vm.box_url = "http://files.vagrantup.com/lucid32_old.box"
    
      config.vm.provision :chef_solo do |chef|
        # point Vagrant at the location of cookbooks you are going to use,
        # for example, a clone of your fork of github.com/travis-ci/travis-cookbooks
         chef.cookbooks_path = "cookbooks/vagrant_base"
    
        # Turn on verbose Chef logging if necessary
        # chef.log_level      = :debug
    
        # List the recipies you are going to work on/need.
        chef.add_recipe     "build-essential"    
        # chef.add_recipe     "networking_basic"    
        # chef.add_recipe     "kerl"
    
        # chef.json.merge!({
        #   :kerl => {
        #     :user     => "vagrant",
        #     :group    => "vagrant"
        #   }
        # })
      end
    end

Then provision your VM (this will run chef-solo and converge VM to the state you want using Chef recipes you chosen to use):

    vagrant provision


Running chef-solo may take from several seconds to several minutes, dependeing on what selected recipes do. Building 13 Ruby versions/implementations
for travis-ci.org, for example, takes slightly over 50 minutes but most of recipes run in under 15 seconds.


## Developing a new cookbook with Sous Chef

TBD


## Copyright

Michael S. Klishin & Travis CI Development Team, 2011


## License

The MIT license. See LICENSE file in the repository root.
